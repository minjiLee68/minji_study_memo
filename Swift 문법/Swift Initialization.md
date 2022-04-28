# init (Swift Initialization) 초기화
Initialization - 초기화는 class, structure, enumeration과 같은 named type의 stored property의 값을 초기화 하는 것 이다.

예시를 들기 위해 하나의 structure Pet을 정의한다.

    struct Pet {
        code..
    }

## Default Initializer
    let myPet = Pet()

Pet에는 아직 initializer가 정의되어 있지 않지만, 자동으로 default initializer가 사용된다. 

만약 사용하고자 하는 타입이 stored property가 없거나, 모든 stored property가 default 값이 있다면 default initializer를 사용할 수 있다.

struct Pet에 정의 부분에 몇가지 stored properties를 추가해보자

    struct Pet {
        let name: String = "joy"
        let isDog: 
        Bool = true
    }

두 개의 stored properties가 추가 되었지만. 여전히 default initializer는 작동을 한다.

그 이유는 name, isDog 모두 default 값을 가지고 있기 때문

optional stored property가 추가되면 어떻게 될까?

    struct Pet {
        let name: String = "joy"
        let isDog: Bool = ture
        var age: Int?
    }

여전히 age의 값이 nil로 초기화 되면서 default initializer가 작동된다.

하지만 age가 constant라면 nil로 초기화 되지 않기 때문에 nil이라는 초기 값을 지정해줘야 한다.

## Memberwise Initiallizer - 멤버별 초기화

타입의 모든 stored properties의 값을 초기화 하거나, 상수로 사용하지 않는 경우가 많기 때문에, 타입의 각 멤버별로 초기화 하는 방법

    struct Pet {
        let name: String
        let isDog: Bool
        let age: Int
    }

    위에서 사용한 동일한 예시이지만 이번에는 모두 default 값이 없고, 정의되어 있는 initializer역시 없다.

    하지만 Swift structure은 자동으로 Memverwise Initializer를 만들어 주기 때문에, 각 멤버별 초기값을 지정해 줄 수 있다.

        let myPet = Pet(name: "joy", isDog: true, age: 10)

    하지만 default 값이 있는 멤버가 있다면, Memberwise Initializer는 그 멤버의 값을 파라미터로 받지 않는다.

    struct Pet {
        let name: String
        let isDog: Bool = true
        let age: Int
    }

    let myPet = Pet(name: "joy", age: 10)

이번에는 custom initializer를 추가해보자

    struct Pet {
        let name: String
        let isDog: Bool
        let age: Int

        init(name: String, age: Int) {
            self.name = name
            self.age = age
            self.isDog = true
        }
    }

이렇게 struct정의 중괄호 안에 initializer를 정의하게 되면 더 이상 Memverwise Initializer는 자동으로 생성되지 않는다.

만약 custom initializer와 Memberwise initializer를 둘다 사용하고 싶다면, 

    struct Pet {
        let name: String
        let isDog: Bool
        let age: Int
    }

    extension Pet {
        init(name: String, age: Int) {
            self.name = name
            self.age = age
            self.isDog = true
        }
    }
