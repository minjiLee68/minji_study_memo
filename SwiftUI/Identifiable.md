# Identifiable
## 인스턴스가 안정적인 ID를 가진 entitiy의 값을 보유하는 유형의 클래스

    한마디로 식별 가능하게 하는 프로토콜이다.

이 프로토콜이 어떻게 이뤄져 있는지 살펴보자.

<img src="https://user-images.githubusercontent.com/88191880/170877563-6db201e0-9afa-4efa-9f2d-53149c431afb.png" width="500" height="200"/>

associatedtype으로 id가 선언되어 있다.

그리고 이는 Hashable을 준수한다.
Hashable프로토콜을 준수하기 때문에 hashValue를 갖게 된다.

identifiable을 통해 앱과 앱의 데이터베이스 저장소에서 안정적인 속성으로 User유형을 정의할 수 있다. 사용자 이름과 같은 다른 데이터 필드가 변경되더라도 이 속성(id)을 사용하여 특정 사용자를 식별할 수 있다

ID는 다음과 같은 특성을 가질 수 있다.

1. UUID와 같이 항상 고유함을 보장한다.
2. 데이터베이스 레코드 키와 같이 환경별로 영구적으로 고유하다.
3. object의 수명 동안 고유하다.

--------------------

### SwiftUI에서 identifiable은 자주 쓰이기 때문에 반드시 알아둬야 할 프로토콜이다.

#### identifiable 프로토콜이 생겨난 배경 ?

#### 만약 앱 내에 다음 Product 구조체타입을 정의했다고 가정해보자

    Struct Product {
        let name: String
        let price: Int
    }

#### 그리고 Product 타입을 사용하여 제품명과 가격이 같은 객체를 생성해보도록 하자

    let PS5 = Product(name: "controller", price: 100)
    let PS4 = Product(name: "controller", price: 100)

#### 이것을 Equatable 프로토콜을 채택하고 비교해보면 서로 다른 종류의 제품을 의도했더라도 같은 타입에 이름과 가격이 모두 같으므로 결과는 true가 된다.

#### 그런데 만약 고유 값을 통해 각각을 구분할 수 있도록 id 프로퍼티를 추가해준다면?? >>

    Struct Product {
        let name: String
        let price: Int
        let id: Int
    }

    let PS5_1 = Product(id: 1, name: "controller", price: 100)
    let PS5_2 = Product(id: 1, name: "controller", price: 120)

#### 이렇게 비교해보면 PS5_1과 PS5_2는 false이다. 두 개가 같은 제품이라고 해도 가격이 달라졌기 때문에 같지 않다는 결과가 나온다.

#### 하지만 이 제품의 동일 여부를 판단하려면 id 값을 비교해보면 된다.

    PS5_1 == PS5_2 // false

    PS5_1.id == PS5_2.id // true

---------------

### Identifiable 프로토콜은 Hashable 프로토콜을 준수하는 id 프로퍼티 하나만 가지는 아주 단순한 프로토콜 이다.

#### 이 프로토콜을 채택한 타입은 고유 객체를 구분하기 위해서 비교 알고리즘에 이 id를 사용하게 되고 id가 다르다면 서로 다른 대상이 되는 것이면 값이 다르더라도 id가 같으면 동일한 개체로 구분되게 되는 것이다.

#### 따라서 Product가 Identifiable을 채택했다면 앞에서 구현했던 id가 필수가 되는 것이다.

#### 각 개체는 특정 상태와 관계없이 언제나 id에 의해 고유 개체로 구분될 수 있는 것이다.

--------------
### SwiftUI에서 List나 ForEach처럼 데이터를 나열하는 뷰 또는 alert, actionSheet처럼 화면을 띄울 항목을 정확히 구분지어야 할 때 Identifiable 프로토콜을 요구하게 된다.

#### 따라서 여기에 사용되는 데이터는 데이터 간 식별이 가능하도록 id를 제공해야 한다.

#### 예를 들어서 List에 identifiable 프로토콜을 적용하지 않았을 경우에 컴파일러가 어느 것을 식별자로 사용해야 하는지 알 수 없기 때문에 id로 사용할 항목을 매게 변수에 전달해줘야 한다.
