# SOLID
SOLID원칙이란 객체지향 설계에 더 좋은 아키텍쳐를 설계하기 위해 지켜야 하는 원칙들의 5가지를 앞의 약어만 따서 정리한 단어이다.

solid 원칙을 알아야하는 이유?

기능 구현도 중요하지만, 새로운 기능이 추가되거나, 유지보수가 되어야할 때 더욱 생산성 있고 유연하게 대처가 가능해야한다.

이를 위해서 코드를 어떻게 설계하고 개발해나가는지가 중요하다.

solid원칙은 이러한 좋은 설계를 위한 원칙들을 정리해주기 때문에 중요시된다.

-------

## SRP(Single Responsibility Principle) - 단일 책임 원칙

클래스나 함수를 설계할 때, 각 단위들은 단 하나의 책임만을 가져야 한다는 원칙

예를 들어 강아지는 멍멍, 고양이는 야옹이라 할 수 있다.

### 안좋은 예
    class LoginService {
        func login(id: String, pw: String) {
            let userData = requestLogin()
            let user = decodeUserInform(data: userData)
            saveUserOnDatabase(user: user)
        }

        private func requestLogin() -> Data {
            // call api
            return Data()
        }

        private func decodeUserInform(data: Data) -> User {
            return User(name: "", age: 10)
        }

        private func saveUserOnDatabase(user: User) {
            // save User
        }
    }

위에 예를 보면 LoginService라는 클래스에서 DB, Decoder, APIHandler의 많은 역활을 중구난방으로 가지고 있다.

### 좋은 예

    protocol APIHandlerProtocol {
        func requestLogin() -> Data
    }

    protocol DecodingHandlerProtocol {
        func decode<T>(from data: Data) -> T
    }

    protocol DBhandlerProtocol {
        func saveOnDatabase<T>(inform: T)
    }

    class LoginService {
        let apiHandler: APIHandlerProtocol
        let decodingHandler: DecodingHandlerProtocol
        let dbHandler: DBHandlerProtocol

        init(apiHandler: APIHandlerProtocol,
        decodingHandler: DecodingHandlerProtocol,
        dbHandler: DBHandlerProtocol 
        ) {
            self.apiHandler = apiHandler
            self.decodingHandler = decodingHandler
            slef.dbHandler = abHandler
        }

        func login() {
            let loginData = apiHandler.requestLogin()
            let user: User = decodingHandler.decode(from: loginData)
            dbHandler.saveOnDatabase(inform: user)
        }
    }

안좋은 예와 비교해서 각각의 db, decoder, apihandler 역할을 하는 프로토콜을 만들어주고 각자의 역할만 하는 메소드만을 구현하게 하였다. 그리고 LoginService에서는 단지 이 프로토콜을 활용해서 상호작용만을 하고 있다.

이전과 비교해서 확실히 LoginService는 로그인에 관련된 로직만을 다루는데 각각의 모듈들을 활용해서 로그인에 관한 책임만을 가지고 있다는 것을 알 수 있다.

------

## OCP(Open-Closed Principle) - 개방, 폐쇄 원칙

확장에는 열려있으나 변경에는 닫혀있어야 한다는 원칙이다.

어떤 기능을 추가할 때, 기존의 코드는 만지지 않고 새로 동작하는 기능에 대해서만 코드가 작성이 되어야 한다. 

프로토콜을 활용하면 이러한 원칙을 지키기가 쉽다.

만약 동물의 소리를 내는 동물원이라는 변수가 있는데 여기에 새로운 동물이 추가된다고 생각을 해보다

### 안좋은 예

    class Dog {
        func makeSound() {
            print("멍멍")
        }
    }

    class Cat {
        func makeSound() {
            print("야옹")
        }
    }

    class Zoo {
        var dogs: [Dog] = [Dog(), Dog(), Dog()]

        var cats: [Cat] = [Cat(), Cat(), Cat()]

        func makeAllSound() {
            dogs.forEach {
                $0.makeSount()
            }

            cats.forEach {
                $0.makeSount()
            }
        }
    }

만약 여기에 새로운 동물을 추가하면 어떻게 될까?

class로 새로운 동물을 정의 하고 Zoo에 또 기존의 코드를 만져야 하고 수정을 진행하게 된다. 이렇게 되면 OCP원칙을 어기고 설계가 진행되는 것이다.

### 좋은 예

    protocol Animal {
        func makeSound()
    }

    class Dog: Animal {
        func makeSound() {
            print("멍멍")
        }
    }

    class Cat: Animal {
        func makeSound() {
            print("야옹")
        }
    }

    class Zoo {
        var animals: [Animal] = []

        func makeAllSound() {
            animals.forEach {
                $0.makeSound()
            }
        }
    }

이렇게 프로토콜을 활용해서 설계를 진행하게 되면 새롭게 동물이 추가되면 기존의 코드는 만지지 않고 그저 class 새로운 동물: Animal으로 선언하여 구현만하면 된다.

그렇게 되면 Zoo클래스에서는 기존의 코드는 만지지 않고 새로운 동물들을 추가하여 함수를 동작할 수 있게 되는 것이다.

확장에는 열려있지만 수정에는 닫혀있는 ocp를 지키는 코드가 된다.

------

## LSP(Liskov Substitution Principle) - 리스코프 치환 원칙

부모(super class)로 동작하는 곳에서 자식(sub class)를 넣어주어도 대체가 가능해야 한다는 원칙이다.

자식 클래스를 구현할 때, 기본적으로 부모 클래스의 기능이나 능력들을 물려받는다. 여기서 자식 클래스는 동작을 할 때, 부모 클래스의 기능들을 제한하면 안된다는 뜻이다.

즉, 부모 클래스의 타입에 자식 클래스의 인스턴스를 넣어도 똑같이 동작하여야 한다.

### 안좋은 예

    class Rectangle {
        var width: Float = 0
        var height: Float = 0

        var area: Float {
            return width * height
        }
    }

    class Square: Rectangle {
        override var width: Float {
            did set {
                height = width
            }
        }
    }
    
    func printArea(of rectangle: Rectangle) {
        rectangle.height = 3
        rectangle.width = 6
        print(rectangle.area)
    }

    let rectangle = Rectangle()
    printArea(of: rectangle)
    //18

    let square = Square()
    printArea(of: square)
    //36


실제로 정사각형은 직사각형이라고 할 수 있다. 이 원리에 따라 프로그램을 다음과 같이 설계하게 되면 문제가 생기게 된다. 바로 정사각형의 넓이를 출력해야 할 때, height = width라는 구문으로 인해 printArea(_: Rectangle)에서 원하는 결과를 얻지 못한다.

즉, 부모의 역할을 자식에서 대신하지 못하고 있는 상황이 발생하게 된다.

### 좋은 예

    protocol Shape {
        var area: Float { get }
    }

    class Rectangle: Shape {
        let width: Float
        let height: Float

        var area: Float {
            return width * height
        }

        init(width: Float, height: Float) {
            self.width = width
            self.height = height
        }
    }

    class Square: Shape {
        let langth: Float

        var area: Float {
            return length * length
        }

        init(length: Float) {
            self.length = length
        }
    }

다음과 같은 방법으로 rectangle, square 모두 shape이라는 프로토콜을 채택할 수 있게 설계하고 실제 구현부는 채택하는 하위 클래스로 넘기면은 lsp의 원칙에 어긋나지 않는 프로그램을 설계할 수 있게 된다.

--------

## ISP(Interface Segregation Principle) - 인터페이스 분리 원칙

인터페이스를 일반화하여 구현하지 않는 인터페이스를 채택하는 것보다 구체적인 인터페이스를 채택하는 것이 더 좋다는 원칙이다.

인터페이스를 설계할 때, 굳이 사용하지 않는 인터페이스를 채택하여 구현하지 말고 오히려 한 가지의 기능만을 가지더라도 정말 사용하는 기능만을 가지는 인터페이스로 분리하라는 것이다.

### 안좋은 예

    protocol Shape {
        var area: Float {get}
        var length: Float {get}
    }

    class Square: Shape {
        var width: Float
        var height: Float
    
        var area: Float {
            return width * height
        }
    
        var length: Float {
            return 0
        }
    
    init(width: Float,
        height: Float) {
        self.width = width
        self.height = height
        }
    }

    class Line: Shape {
        var pointA: Float
        var pointB: Float
    
        var area: Float {
            return 0
        }
    
        var length: Float {
            return pointA - pointB
        }
    
    init(pointA: Float,
         pointB: Float) {
        self.pointA = pointA
        self.pointB = pointB
        }
    }

두 클래스 모두 shape을 상속받는 객체이지만 실제로 square는 length라는 변수가 필요가 없고 line은 area라는 변수가 필요없게 된다. 그럼에도 단지 shape이라는 프로토콜을 채택한다는 이유만으로 필요없는 기능을 구현하고 있다.

### 좋은 예

    protocol AreaCalculatableShape {
        var area: Float { get }
    }

    protocol LenghtCalculatableShape {
        var length: Float { get }
    }

    class Square:       AreaCalculatableShape {
        var width: Float
        var height: Float
    
        var area: Float {
            return width * height
        }
    
        init(width: Float,
         height: Float) {
            self.width = width
            self.height = height
        }
    }

    class Line: LenghtCalculatableShape {
        var pointA: Float
        var pointB: Float
    
        var length: Float {
            return pointA - pointB
        }
    
        init(pointA: Float,
         pointB: Float) {
            self.pointA = pointA
            self.pointB = pointB
        }
    }      

기존에 필요없는 기능들을 구현하고 있던 인터페이스들을 더욱 세분화하여 나누어 주었다.

------

## DIP(Dependency Inversion Principle) - 의존관계 역전 원칙

상위 모듈이 하위 모듈에 의존하면 안되고 두 모듈 모두 추상화에 의존하게 만들어야 한다는 원칙이다.

어떤 상위의 모듈에서 하위 모듈을 가지고 있을 때, 사위 모듈의 기능이 하위 모듈에 의존해서 기능을 수행하면 안된다는 뜻이다. 

즉, 추상화를 진행하여 각각의 모듈에 더 추상화된 것에 의존하게 만들어야 한다는 뜻이다. 이렇게 코드를 설계해야 재사용에도 유용하고 하나를 수정했을 때 더욱 수정사항이 많이 없는 훌륭한 프로그램을 설계할 수 있게 된다.

### 안좋은 예

    class APIHandler {
        func request() -> Data {
            return Data(base64Encoded: "This Data")!
        }
    }

    class LoginService {
        let apiHandler: APIHandler = APIHandler()

        func login() {
            let loginData = apiHandler.request()
            print(loginData)
        }
    }

현재 상위 모듈인 loginservice가 하위 모듈인 apihandler에 의존하고 있는 관계로 만약 apihandler의 구현 방법이 변화하게 되면 프로그램에 영향을 미치게 되고 새롭게 loginservice라는 상위모듈을 수정해야하는 상황이 일어날 수 있다. 

이러한 상황이 DIP의 원칙을 어긴 프로그램의 설계라고 할 수 있다.

### 좋은 예

    protocol APIHandlerProtocol {
        func requestAPI() -> Data
    }

    class LoginService {
        let apiHandler: APIHandlerProtocol

        init(apiHandler: APIHandlerProtocol) {
            self.apiHandler = apiHandler
        }

        func login() {
            let loginData = apiHandler.requestAPI()
            print(loginData)
        }
    }

    class LoginAPI: APIHandlerProtocol {
        func requestAPI() -> Data {
            return Data(base64Encoded: "User")!
        }
    }

    let loginAPI = LoginAPI()
    let loginService = LoginService(apiHandler: loginAPI)
    loginService.login()

이렇게 작성하게 되면 loginservice는 기존에 apihandler에 의존하지 않고 추상화 시킨 객체인 apihandlerprotocol에 의존하게 된다. 

그렇게 때문에 apihandlerprotocol의 구현부는 외부에서 변화에 따라 지정해서 지정해주면 되기 때문에 loginService는 구현부에 상관없이 좀 더 변화에 민감하지 않은 DIP의 원칙을 지킨 프로그램을 설계할 수 있다.
