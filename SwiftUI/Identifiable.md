# Identifiable
## 인스턴스가 안정적인 ID를 가진 entitiy의 값을 보유하는 유형의 클래스

    한마디로 식별 가능하게 하는 프로토콜이다.

이 프로토콜이 어떻게 이뤄져 있는지 살펴보자.

<img src="https://user-images.githubusercontent.com/88191880/170877563-6db201e0-9afa-4efa-9f2d-53149c431afb.png" width="500" height="100"/>

associatedtype으로 id가 선언되어 있다.

그리고 이는 Hashable을 준수한다.
Hashable프로토콜을 준수하기 때문에 hashValue를 갖게 된다.

identifiable을 통해 앱과 앱의 데이터베이스 저장소에서 안정적인 속성으로 User유형을 정의할 수 있다. 사용자 이름과 같은 다른 데이터 필드가 변경되더라도 이 속성(id)을 사용하여 특정 사용자를 식별할 수 있다

ID는 다음과 같은 특성을 가질 수 있다.

1. UUID와 같이 항상 고유함을 보장한다.
2. 데이터베이스 레코드 키와 같이 환경별로 영구적으로 고유하다.
3. object의 수명 동안 고유하다.
