# Boolean
- 불(bool) or 불리언(boolean)
- true, false의 단일 값을 가짐
- 비교 수식에 사용
- loop or if문에서 사용
- 비교문을 바로 변수값을 선언하는 부분에서 넣을 수 있다는 것이 좋다.

토글 버튼처럼 버튼이 켜진상태(true), 꺼진상태(false)처럼 생각하면 쉽다..

    let yes = true
    let no = false

boolean 타입은 비교할 떄 많이 쓴다.
    
    let isFourGreaterThanFive = 4 > 5
    if isFourGreaterThanFive {
        print("---> 참")
    } else {
        print("---> 거짓")
    }
    ----> 거짓

    /* if 조건..... {
        // 조건이 참인 경우에 수행하는 코드를 여기다가...
    } else {
        // 그렇지 않은 경우에 코드는 여기다가...
    } */

문자변수 비교와 bool변수 비교
    
    let name1 = "jin"
    let name2 = "jason"

    let isTwoNameSame = name1 == name2

    if isTwoNameSame {
        print("---> 같다")
    } else {
        print("----> 다르다")
    }
    ----> 다르다

## Boolean 추가 개념
- 논리 연산자 
- 삼항 연산자 
------
    논리 연산자 (and / or)
    
    let isJason = name2 == "jason"
    let isMale = true

    let jasonAndMale = isJason && isMale

    (&& -> 둘다 만족해야 true)

    let jasonOrMale = isJason || isMale

    (|| -> 둘 중 하나만 만족해도 true)

--------
    삼항 연산자 (항이 3개..)
    let greetingMessage: String = isJason ? "Hello Jason" : "Hello Somebody"

    맨 앞에가 조건이고, 이 조건이 참이면(?) 왼쪽 : 그렇지 않으면 오른쪽
