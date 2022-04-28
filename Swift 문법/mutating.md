# mutating
스위프트에서 클래스는 참조타입이고 구조체와 열거형은 값 타입이다.

값 타입인 구조체에서는 인스턴스 메소드 내에서 프로퍼티들을 수정할 수 없게 되어 있다. 

따라서 이러한 프로퍼티들을 구조체 안의 메소드에서 수정을 해주기 위해 mutating이라는 키워드를 사용하게 된다.

    mutating
    특정 메소드 내에서 구조테 또는 열거형의 프로퍼티를 수정해야 하는 경우, 해당 메소드의 동작을 변경하도록 하는 것

간단한 코드를 통해 자세히 알아보자

    struct Person {
        let name: String
        var age: Int

        init(name: String, age: Int) {
            self.name = name
            self.age = age
        }

        func changeAge() {
            age = 10 //에러!!
        }
    }

다음과 같은 간단한 구조체를 생성했다. Person이라는 구조체는 name과 age를 프로퍼티로 가지고 changeAge()라는 메소드도 가지고 있다. 

그리고 init을 통해 초기화도 해주었다. 하지만 저렇게 하면 에러가 뜬다.

    Cannot assign to property: 'self' is immutable

Cannot assign property라는 에러가 떴다. 이것이 바로 값 타입인 구조체에서는 메소드안의 값을 수정할 수 없기 때문에 일어나는 것이다.

이럴 때 changeAge()메서드 앞에 mutating을 붙여서 수정할 수 있게 해준다.

    struct Person {
        let name: String
        var age: Int

        init(name: String, age: Int) {
            self.name = name
            self.age = age
        }

        mutating func changeAge() {
            age = 10 //에러!!
        }
    }
