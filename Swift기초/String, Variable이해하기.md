# String and Variable 이해하기
### String? (문자열)
각각의 문자 하나하나의 나열이다.

swift에는 아주 쿨한 기능이 있다.
String Interpolation (문자열 삽입) 

이 기능을 이용하면 변수 값을 문자열에 삽입할 수 있다.
 
    let name = "민지"
    "Hello \(name)"
    --> "Hello 민지"

### Variable? (변수)
어떤 값을 저장할 수 있는 공간이다.

그리고 그 변수에 어떤 값을 채워놓고 그 값을 이용할 때 변수를 사용한다.

object에는 변수들을 많이 가지고 있다.

컴퓨터도 type에 따라 그에 맞는 상자(변수)를 준비한다.
    
    정수형 타입, 문자열 타입 등등..

--------

## 변수와 상수 이해하기
### Variable vc. Constant

    Variable (var) : 값을 수정할 수 있다.
    Constant (let) : 값을 수정할 수 없다.

Swift에서는 언제 어디서 값이 바뀔지 모르는 변수보다는 상수를 사용하는 것을 권장하고 있다.

    var name = "jin"
    let birthyear = 2001

만약 이름을 바꾸고 싶다면 바꿀 수 있지만, 태어난 해를 바꾸려 한다면 컴파일 에러가 발생한다.

--------

Swift는 정적 타이핑 언어이다.
변수나 상수를 정의할 때 그 자료형(타입)이 어떤 것인지를 명시해주어야 하는 언어이다. (Type Annotation)

근데 고맙게도 swift에서는 타입추론을 해준다. (Type Inference)

Swift에서는 타입을 굉장히 엄격하게 다루기 때문에, 다른 자료형끼리는 기본적인 연산조차 되지 않는다.

    Int 타입인 birthyear와 Float 타입인 height을 더하려고 하면 컴파일 에러 발생

    birthyear + height 

이럴 때는 명확하게 다음과 같이 사용해야 한다.

    Float(birthyear) + height
