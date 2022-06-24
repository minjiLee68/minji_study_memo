# GCD (Grand Central Patch) -2
GCD -1 내용을 요약하자면...

1. task들을 큐로 보냄
2. 큐의 특성이 serial이냐 concurrent에 따라 task를 하나의 혹은 여러개의 스레드로 보냄

실제로 iOS에서 제공하는 DispatchQueue의 종류는 세가지가 있다.

1. 메인 큐
2. 글로벌 큐
3. 커스텀(프라이빗)큐

큐의 종류에 따라 특성이 다르기 떄문에 작업의 특성, 원하는 일처리에 따라 대기열(큐)의 특성에 맞게 작업을 넣어야 한다.

## Main Queue
- 오직 한개만 존재
- Serial 특성을 가진 Queue
- 이곳에 할당된 task는 메인 스레드에서 처리 (UI업데이트 내용 처리)

코드 작성할 때 별도의 처리를 하지 않는 이상 모든 일들은 메인 스레드가 작업하게 된다.

메인 스레드에서 작업된다는건 이 task들이 우선 메인 큐에 할당 됨을 의미한다.

1. 메인 큐에서 task는 메인 스레드로만 보냄 (serial 특성)
2. 메인 스레드는 오직 한개 밖에 없음
3. 따라서 메인 큐에 있는 task들을 다른 곳에 분산시킬래도 분산 시킬만한데가 없음
4. 큐의 특성은 무조건 Serial이 됨 (분산은 concurrent의 특징)

## Global Queue
- Concurrent 특성을 가진 Queue
- Qos (Quality Of Service)에 따라 여러개의 종류로 나뉨 (6종류)

메인 큐와 달리 글로벌 큐는 concurrent이다. 그러면 여러개의 스레드로 task를 분산시킬테니, 작업을 보낼 떄 순서가 중요하지 않은 것들을 글로벌 큐로 보내면 된다.

클로벌 큐는 qos를 지정함으로써 작업의 중요도를 결정할 수 있다.

    DispatchQueue.global(qos: .utility).async {

    }

### Qos의 종류
 - userInteractive: 
    - 사용자와 직접 상호작용 하는 직업. (ex. UI업데이트, 애니메이션 등)
    - 사용자의 행동에 대한 즉각적인 반응이 기대 되지만, 이를 메인 스레드에서 처리하면 많은 로드가 걸리는 작업들을 userInteractive에서 처리해서 바로 동작하는 것처럼 보이게 함

- userInitiated:
    - 클릭할 때 작업을 수행하는 것과 같은 즉각적인 결과가 필요한 작업. (ex. 저장된 문서 열기)
    - 하지만 userInteractive 보다는 조금 오래걸릴 수 있고 유지가 어느정도 이를 인지 하고 있음.

- default:
    - 일반적인 작업
- utility:
    - 보통 progress bar와 함께 길게 실행되는 작업.(ex. 데이터 다운로드)
- background: 
    - 유저가 직접적으로 인지하지 않는 시간이 덜 중요한 작업 (ex. 동기화 및 백업)
- unspecified:
    - Qos정보가 없음을 나타냄. 거의 사용할 일 없다.


## Custom Queue
- 커스텀으로 만듦
- 디폴트로 Serial 특성을 가진 Queue. 하지만 Concurrent로 설정 가능.
- Qos 설정 가능

이렇게 생성할 때 인자에 label을 붙이면 custom queue가 된다. 

    let customSerialQueue = DispatchQueue(label: "minji")

여기에서 concurrent로 설정 가능하다.

    let customSerialQueue = DispatchQueue(label: "minji", attributes: .concurrent)

qos를 따로 설정해주지 않으면 os가 알아서 추론한다. (물론 설정가능하다.)

--------

# GCD 사용시 주의 사항
## 1. UI는 main 스레드에서 처리한다.
메인 스레드가 UI를 담당하는 것은 비단 iOS에만 국한된 것이 아니라, 모든 OS에 적용되는 사항이라고 한다.

(하나의 스레드(메인)에서 UI를 잘 처리하고 있는데 다른 스레드에서 나도 할게..:)하고 끼어들면 UI가 의도치 않게 보일 수 있으니깐 어떻게 보면 당연한 얘기이다.)

이미지 등을 global에서 다운 받아오더라도, 해당 데이터를 UIImageView에 넣어서 UI업데이트 시켜주는 작업은 main에서 해야한다.

    URLSession.shared.dataTask(with: url) { (data, response, error) in
        guard let data = data, error == nil else { return }
        DispatchQueue.main.async() {
            self.imageView.image = UIImage(data: data)
        }
    }.resume()

URLSessiondms opertation queue이고, 모든것이 background thread에서 동작하기 때문에 
이미지 표시는 DispatchQueue.main에서 해준다.

    DispatchQueue.main.async() {
        self.imageView.image = UIImage(data: data)
    }

## 2. sync메소드에 대한 주의 사항 2가지
### 2-1. 메인 큐에서 다른 큐로 작업을 보낼 떄 sync를 사용하면 안된다.

    // in Main Thread
    DispatchQueue.global().sync {

    }

메인 스레드에서는 다른 큐로 보낼떄 .sync를 사용하지 말라는 말이다.

sync로 보낸다는 것은 해당 작업이 끝날때까지 "기다린다"라는 의미이다. 그런데 메인 스레드는 UI를 업데이트 해줘야 하는 친구인데 댜른 작업이 끝날때까지 기다린다는 것은 해당 작업이 끝날 때까지 UI업데이트가 지연된다는 의미이고

결국 화면이 버벅여 보일 것이다.

그러니 메인 스레드에서는 항상 async(비동기)로 작업을 보내주어야 한다.

### 2-2. 현재와 같은 큐에 sync로 작업을 보내면 안된다.

    DispatchQueue.global().async {
        task a
        DispatchQueue.global().sync {
            task b
        }
    }

같은 큐에 동기적으로 작업을 보낸다는건 바로 이런 코드이다. 보다시피 global queue안에서 같은 global queue에 sync로 작업을 보내고 있다. 

우선 task a를 global queue로 보냈기 때문에, 해당 작업은 적당한 스레드에 할당 후 수행 된다.

근데 task a를 수행하다 보니 이 안에는 task b를 global queue에 보내는 작업이 들어있다.

하지만 보내는 방식이 sync 이기 때문에 task b가 스레드에 할당되어 작업이 끝날때까지는 다음 동작을 수행하지 않고 기다리게된다.

- task a를 수행하고 있었던 thread2는 task b가 끝날때까지 기다리느라 멈춰있는다.
- GCD는 global queue에 들어온 task b를 어디 thread에 할당할지 고민한다.
- 할당된 스레드에서 작업을 해야하는데 멈춰있는 스레드이다. -> 이 스레드는 이제 데드락 상태가 된다.

큐에서 사용하고 있는 쓰레드 객체는 정해져 있다. 예를 들어 디폴트 글로벌 큐는 쓰레드 2,3,4번만 사용하고,

백그라운드 글로벌큐는 쓰레드 5,6번만 사용하는 식이다.

따라서 같은 큐에 보내면 같은 스레드에 배치될 수 있는데, 해당 스레드가 sync로 인해 멈춰있는 상황이라면 데드락 상황이 발생한다. 

하지만 qos에 따라 각각 다른 큐 객체를 생성하기에 각각 다른 qos큐라면 쓰레드가 겹칠일이 없기 때문에 데드락 발생 가능성이 없다.

    //데드락 발생 가능성 있음
    DispatchQueue.global().async {
        DispatchQueue.global().sync 
    }

    //데드락 발생 가능성 없음
    DispatchQueue.global(qos: .utility).async {
        DispatchQueue.global().sync 
    }

### 2-3 메인 스레드에서 DispatchQueue.main.sync를 사용하면 안된다.
- DispatchQueue.main: main 큐로 테스크로 보낸다. 이떄 main 큐는 직렬 큐이기 때문에
task를 동일한 스레드 (메인 스레드)에만 할당한다.
- .sync: 해당 task가 끝날때까지 메인 스레드는 일단 기다린다.

아래와 같은 플로우로 상황이 진행된다.
1. 메인 스레드에서 끝날때까지 기다리면서 task를 메인 큐에 보낸다.
2. 메인 큐의 task는 메인 스레드로 할당 (직렬 큐)
3. 근데 메인 스레드는 기다리고 있는 상태
4. 결국 아무것도 진행되지 못하는 데드락 상황 발생

------------

## 3. 객체에 대한 캡처를 주의한다.
동작해야 할 task를 queue에 보낸다는 것은 결국 클로저 보내는 것이다.

따라서 객체에 대한 캡쳐 현상이 발생할 수 있게 되고, 자칫하면 retain cycle이 생길 수도 있다.

## 4. 비동기 작업에서 컴플리션 핸들러의 존재 이유 (completion handler가 필요한 이유)
.async를 통해 비동기로 작업을 보낼 수 있었다. 비동기로 보낸다는 뜻은 해당 작업이 끝날때까지 기다리지 않고

남아있는 다른 작업을 실행하겠다는 의미였다. 다른 작업을 하다가도 보낸 작업이 끝났다면, 그 시점을 파악해서 

마저 필요한 작업을 해줘야 한다.

그렇다면 보낸 작업이 끝났을 때를 어떻게 알 수 있을까? 바로 컴플리션 핸들러를 통해서 알 수 있다.

즉, completion handler는 어떤 작업이 끝났음을 알리는 클로저로 비동기 함수에는 항상 컴플리션 핸들러가 있다고 생각하면 된다.





