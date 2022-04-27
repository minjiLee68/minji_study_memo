# Tuple
사전적 의미 : 튜플(Tuple)이란 유한 개의 사물의 순서있는 열거

Swift에서의 튜플은 다양한 값(데이터)의 묶음이다.

튜플의 구성 요소들은 서로 가른 타입이 가능하며 개수도 사용하고 싶은 만큼 사용이 가능하다.

    let coordinates = (4, 6)
    // let coordinates = (Int, Int)

    let x = coordinates.0
    let y = coordinates.1

x = coordinates.0 이런식으로 해도 되지만 좀 더 명시적으로 표현하기 위해서는

    let coordinatesName = (x: 2, y: 3)
    let x = coordinatesName.x

이렇게 표현할 수 있다.

    let (x,y) = coordinatesName

이렇게도 가능

다양한 데이터 값도 넣어줄 수 있다.

    var tuple = ("minji", 22, true)

    var (name, age, bool) = tuple
    print("이름 : \(name)")

함수 타입도 저장 가능하다.

    func a() -> Int { return 1 }
    func b() -> String { return "Bill" }
    func c() -> Bool { return false }

    var tuple = (a(), b(), c())

튜플은 기본 swift내에 명명된 데이터 타입 외에도 새롭게 선언한 튜플 타입도 저장할 수 있다.

    var tuple2 = (1, (tuple))

## Tuple의 장점
    1. 다양한 데이터 타입을 담는 배열을 만들 수 있다.
        타입 제한없이 다양한 데이터를 담는 배열을 가질 수 있다.
    2. 구조체의 대체가 가능하다.
        기존 구조체보다 훨씬 간단한 형태를 통해 구조체처럼 사용할 수 있다.
    3. 멀티 리턴 함수를 만들 수 있다.
        함수에서 사용 시 하나 이상의 값을 리턴하는 함수를 만들 수 있다.

    func plusAndMinus(a: Int, b: Int) -> (Int, Int) {
        return (a + b, a - b)
    }
    let (plusResult, minusResult) = plusAndMinus(a: 1, b: 2)
