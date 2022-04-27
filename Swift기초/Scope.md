# Scope
** Local 변수: method에서만 사용되고 끝나느 변수
** Instance 변수: Object 내에서 전반적으로 사용되는 변수

## Local Scope
scope: 변수가 사용되는 범위

{ ... scope ... }

코드 블럭 안에 있는 것이 하나의 scope 
그 scope안에서 설정된 변수는 scope이 그 안에 있다고 볼 수 있다.

    ex) 알바 시급 계산

    var hour = 50 // 일 한 시간
    let payPerHour = 10000 //시급
    var salary = 0

    if hours > 40 {
        let extraHours = hour - 40
        salary += extraHours * 2
        hours -= extraHours
    } // scope { 안에서 설정된 extraHours 변수는 바깥에서 사용 x }

    salary += hours * payPerHour

Scope 5가지 규칙

    1. 동일한 스코프의 변수와 상수에 접근할 수 있다.
    2. 동일한 스코프에서는 이전에 선언한 변수와 상수게 접근할 수 있다.
    3. 로컬 스코프에서는 자신의 상위 스코프의 변수와 상수에 접근할 수 있다.
    4. 상위 스코프에서는 하위 스코프에 접근할 수 없다
    5. 서로 다른 스코프의 동일한 상수와 변수가 존재한다면 가장 인접한 곳에 있는 변수와 상수에 접근할 수 있다.
