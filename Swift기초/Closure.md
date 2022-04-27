# Closure
### Closure = { }
클로져는 쉽게 말해서 실행 가능한 코드 블럭이다. 이것을 변수처럼 함수에도 넘겨줄 수 있다. 

클로저는 두 가지 종류가 있는데

    Named Closure
    Unnamed Closure

    func doSomething() { }
    --> 이름이 있는 함수(Named Closure)

    let closure = { }
    --> 이름을 붙이지 않고 사용하는 함수 (Unnamed Closure)

근데 보통 Closure라고 하면 Unnamed Closure를 말한다고 한다.

---------
## 클로저 표현식
    { (parameters) -> Return Type in

        실행 구문
    }

익명 함수인 만큼 func이란 키워드를 쓰지 않는다.

클로저는 클로저 헤드와 클로저 바디로 이루어져 있다.
    
    Closure Head
    { (parameters) -> return type in

        실행구문 Closure body
    }

이 둘을 구분지어주는 게 바로 in 이라는 키워드이다.

실제 어떤 식으로 사용하는지를 봐보면..

### Parameter와 Return Type이 둘다 없는 클로저

클로저는 익명이긴 하지만 함수이다. 따라서 swift 1급 객체이기 때문에, 다음과 같이 상수에 클로저를 대입할 수 있다.

특히 parameter와 return type이 둘 다 없는 경우는 다음과 같이 사용한다.

    let closure = { () -> () in 
        print ("closure")
    }

### Parameter와 Return Type이 있는 클로저

    let closure = { (name: String) -> String in
        return "Hello, \(name)"
    }

함수와 비슷해 보이지만 한 가지 주의해야할 점이 있다.

클로저에선 Argument Label을 사용하지 않는다.
