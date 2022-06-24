# GCD (Grand Central Patch) -1
컴퓨터에는 많은 노동자(Thread)들이 있다. 그리고 일거리(task)도 있다.

보통 코드 작성할 때 별도의 처리를 안해주면 task들은 메인 스레드에서 처리한다.

무거운 작업까지 메인스레드로 넘어가게 되면 무거운 작업들로 인해 화면이 버벅거리는 일이 생긴다.

그래서 Thread에게 task를 분배해주어야 한다.  (aka 동시성 프로그래밍)

메인스레드에 몰린 작업들을 다른 스레드에서도 동시에 작업 하도록 하는 것이 동시성 프로그래밍 이다.

ios에서 제공해주는 queue(aka 대기 행렬)에다 작업을 보내기만 하면 알아서 os가 다른 스레드로 분산 처리를 해준다

queue에 작업을 보내고 난 후 메인 스레드의 행동은 어떨까??

-------------

## 비동기(async), 동기(sync)
### 비동기(async):
메인 스레드가 queue에 보낸 작업에 대해서는 더 이상 신경 쓰지 않고 바로 다음 일을 한다. (시간 절약)

    DispatchQueue.global().async {
         // task
    }

- DispatchQueue: iOS에서 동시성 프로그래밍을 돕기 위해 제공하는 queue
- global: DispatchQueue의 종류
- async: 비동기

### 동기(sync):
메인 스레드가 queue에 보낸 작업이 완료 될 때까지 기다린 후 다음 일을 한다.

    DispatchQueue.global().sync {
        // task
    }

동기적으로 보내는 코드를 짜더라도 실질적으로는 메인쓰레드에서 일을 처리한다..

## 비동기 처리가 필요한 이유?!
### - 시간 절약이 된다.
시간이 많드는 작업의 대부분은 서버와의 통신이기 때문에 네트워크와 관련된 작업들은 내부적으로 비동기적으로 구현 되어있다.

    URLSession.shared.dataTask(with: request) { (data, response, error) in
 
    }

이렇게 URLSession으로 통신을 하게 된다면, 내부적으로 다른 스레드 사용 + 비동기로 구현이 되어 있는 걸 우리가 사용하는 것이다.

그래서 우리가 네트워킹을 할 때 따로 비동기 처리를 안해줘도 내부적으로 구현이 되어있다!

-----------------

## Serial vs Concurrent(직렬 vs 동시)
우리는 queue.async { task } 또는 queue.sync { task }를 통해 task를 queue에 보냈다.

그리고 쌓여있는 task들을 다른 스레드로 보내줘야 하는데 이때, GCD 혹은 Operation의 선택은 이러하다.

### 1. 한개의 스레드에 몰아 넣는다. 
### 2. 여러개의 스레드에 나눈다.

둘 다 가능은 하지만, 둘 중 어떤 방식을 선택할건지가 바로 queue의 특성에 따라 결정된다.

queue의 특성은 Serial(직렬)와 Concurrent(동시)이 있다.

## 1. Serial(직렬)
Serial Queue -> (보통 메인 스레드에서)분산 처리 시킨 작업을 "다른 한개의 스레드에서" 처리하는 큐

## 2. Concurrent(동시)
Concurrent Queue -> (보통 메인 스레드에서)분산 처리 시킨 작업을 "다른 여러개의 스레드에서" 처리하는 큐

몇개의 스레드로 분산할지는 시스템이 알아서 결정한다.

--------

### 그럼 언제 Serial큐를 쓰고 언제 Concurrent큐를 쓸까?
어떤 큐를 사용할 것인지에 대한 핵심 포인트는 바로 작업 순서의 중요도에 있다.

Serial 큐에 담긴 작업들은 오직 하나의 스레드에만 분배된다. 모든 작업들이 그 전 작업이 끝나길 기다렸다가

하나씩 실행되기 때문에 task의 시작과 종료에 대한 순서 예측이 가능하다.

반면 Concurrent 큐에 담긴 작업들은 여러개의 스레드로 분배된다. 선입선출이라는 Queue의 특성상 작업들이 순서대로

분배되어 실행되긴 하겠지만, 그것들이 끝나는 순서는 알 수 없다.

예를 들어 각 cell에서 이미지를 로드해야 한다고 할 떄, 어떤 데이터가 먼저 들어와야 할지 순서는 중요하지 않다.

빠르면 좋은 거! 이런 상황에서는 Concurrent Queue를 사용하다.

하지만 반대로 순서가 중요한 작업들을 처리해야 한다면 Serial Queue를 사용한다.

-------------

## (async / sync) != (serial / concurrent) 서로 별개의 개념!
### aync vs sync
작업을 보내는 시점에서 기다릴지 말지에 대해 다루는 것

### concurrent vs serial
queue(대기열)로 보내진 작업들을 여러개의 스레드로 보낼 것인지 한개의 스레드로 보낼 것인지에 대해 다루는 것

### 4가지의 조합으로 이해해보자
- SerialQueue.sync: 메인 스레드의 작업 흐름이 queue에 넘긴 테스크가 끝날때까지 멈춰있고(sync), 넘겨진 task는 queue에 먼저 담겨있던 작업들과 같은 스레드에 보내지기 때문에 해당 작업들이 모두 끝나야 실행(Serial Queue)

- ConcurrentQueue.sync: 메인 스레드의 작업 흐림이 queue에 넘긴 테스크가 끝날 때까지 멈춰있고(sync), 넘겨진 task는 queue에 먼저 담겨있던 작업들과 다른 스레드에 보내질 수 있기 떄문에 해당 작업들이 모두 끝나지 않아도 실행(concurrentQueue)

- SerialQueue.aync: 메인 스레드의 작업 흐름이 태스크를 queue에 넘기자마자 반환되고(async), 넘겨진 task는 queue에 먼저 담겨있던 작업들과 같은 스레드에 보내지기 때문에 해당 작업들이 모두 끝나야 실행(Serial Queue)

- ConcurrentQueue.async: 메인 스레드의 작업 흐름이 태스크를 queue에 넘기자마자 반환되고(async), 넘겨진 task는 queue에 먼저 담겨있던 작업들과 다른 스레드에 보내질 수 있기 때문에 해당 작업들이 모두 끝나지 않아도 실행(concurrentQueue)
