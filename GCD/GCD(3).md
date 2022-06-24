# GCD (Grand Central Patch) - 3
## Dispatch Group
작업 (task)을 Dispatch Queue에 보내면, GCD가 스레드를 적절히 생성해서 분배해준다.

이때 여러 스레드로 분배된 작업들이 끝나는 시점을 각각 파악하는 것이 아니라, 하나로 그룹지어서 한번에 파악하고 싶을 때 DispatchGroup의 개념이 사용된다. 

즉, 그룹으로 묶인 작업의 마지막 시점을 파악하는 것이다! 

실제 상황을 예로 들면 모든 이미지 다운로드가 완료된 시점에 특정 행동을 해야 한다면 해당 시점을 캐치하기 위해

Dispatch Group의 개념을 사용할 수 있습니다. 

## Dispatch Group 사용 방법
1. DispatchGroup 생성

        let group = DispatchGroup()

2. async 메소드로 task를 보낼 때, group 파라미터에 생성한 그룹 추가

        DispatchQueue.global().async(group: group1) { task }

    즉 큐로 보낼 때 어떤 그룹에 넣을 건지 정해 주는 것.

    이때 task를 다른 큐로 보내더라도 같은 그룹으로 지정할 수 있다.

        DispatchQueue.global(qos: .utility).async(group: group1) { task }
        DispatchQueue.global().async(group: group1) { task }

3. notify(queue: )를 통해 notification block(그룹으로 묶인 모든 작업이 끝났을때 실행될 작업)을 넘김

        // main queue에서 notification block을 실행한다.
        group1.notify(queue: .main) { [weak self] in 
            self?.myLabel.text = "Group1으로 묶인 모든 작업이 끝났습니다."
        }

    이때 queue 파라미터는 해당 작업을 어느 곳에서 실행할지를 정한다.
    만약 group1로 묶인 모든 task가 다 끝났거나, 아예 들어오지 않아 비어있는 상태라면 notification block은 바로 실행된다.

---------
