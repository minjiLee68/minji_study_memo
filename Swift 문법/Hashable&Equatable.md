# Hashable이 무엇이고, Equatable을 왜 상속해야 할까?

## Hashable
### 정의
Hashable은 "정수 hash 값을 제공하는 타입"으로 정의된 프로토콜이다.

hash란, 해시 함수에 의해 얻어지는 값으로 해시값, 해시코드, 해시 체크섬으로도 불린다.

해시 함수 - 임의의 길이의 데이터를 고정된 길이의 데이터로 매핑하는 함수 

hashValue - 어떠힌 데이터를 Hash 함수에 넣게 되면 반환해주는 값

HashTable에서 hash값을 찾기 위해선 key가 필요하고, 이 key는 식별할 수 있도록 unique 해야 한다.

## 구현
Hashable를 만들어 주기 위해선 2가지가 필요하다.

    1.Equatable에 있는  == 함수 구현
    2. HashValue를 만들기

Hasher는 HashValue를 생성해주는 해시함수가 있는 struct로, combine메서드를 가지고 있다.

이 메소드를 사용하여 식별할 수 있는 identifire를 hasher에게 넘겨주면 된다.

## 언제 사용?

    - Hashable 프로토콜을 준수하는 모든 유형은 set, dictionary key로 사용
    - Hashable을 준수하고 있는 타입: String, Integer, floating-point, Boolean
    - Optional과 Array, Range들은 argument들이 동일하게 구현될 때 자동으로 Hashable이 된다.
    - custom 타입에도 Hashable을 준수하도록 할 수 있다.
    - associated value 없이 enumdmf 정의하면 자동으로 Hashable을 준수한다.
    - hash(into:)메서드를 구현한다면 다른 custom 타입도 Hashable을 준수할 수 있다.
    - 저장 property들이 모두 Hashable한 구조체, 모두 Hashable한 enum 타입을 가진 enum이라면 컴파일러가 hash(inro:)메서드를 자동으로 제공한다.

Hashable은 Equatable protocol을 상속받고 있다.

---------

# Equatable

    protocol Equatable {
        static func == (lhs: Self, rhs: Self) -> Bool
    }

## 정의
Equatable은 값이 동일한 지 어떤지를 비교할 수 있는 타입인 프로토콜이다.

이 프로토콜을 준수하는 타입은 == 혹은 != 사용하여 동등성을 비교할 수 있다.

## 왜 Hashable은 Equatable을 상속해야 할까?
hashValue는 고유값이어야 하므로 고유값인지 식별해 줄 수 있는 "=="함수가 필요하다.

그 함수는 Equatable 프로토콜 안에 들어있다.

따라서 Hashable이 Equatable을 상속해야 한다.

## 다시 정리
해시 값은 각각 타입의 인스턴스를 식별하는 값으로 유일성을 가지고 있어야 한다. 해시 값이 유일한 값인지를 비교해야하기 때문에 Equatable 프로토콜을 상속 받아 값을 비교할 수 있도록 하고 있다.
Set, Dictionary의 Key는 중복을 허용하지 않아야 함으로 Equatable을 상속하고 있는 Hashable을 준수해야 한다.

## 기본 데이터 타입도 Equatable 따름

Swift 표준 라이브러리 대부분의 "기본 데이터 타입 Int, String, Double등" Hashable 타입을 상속받음

---> 따라서 Equatable를 따름 -> 우리는 기본 데이터 타입에서 자연스럽게 ==을 사용 가능 
