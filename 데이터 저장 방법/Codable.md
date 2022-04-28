# Codable
ios앱 내에서 어떻게 데이터를 저장하고 유지할 수 있는지 간단한 앱을 통해서 알아보았다. 

ios앱에서 데이터를 저장할 수 있는 방법은

- NSCoding
- Property List
- Serialization
- Core Data
- Realm

많고 복잡한 데이터를 다루기 위해서는

Core Data, Realm

Core Data는 구현 난이도가 있고, 관리도 난이도가 있어서 초보자가 어려움을 느낀다.

작고 덜 복잡한 데이터를 다루기 위해서는

NSCoding, Property List

그런데 Swift4부터 Codable이 나왔다.
NSCoding처럼 간단하고 적은 데이터를 관리하기에 매우 적합한 기능들을 제공해준다.

json 형태를 아주 쉽게 다룰 수 있게 해주는 프로토콜이다.

-------------------
# Codable을 사용하여 일기 앱 만들기

맨 처음 Diary객체를 만들어준다.
Diary객체는 최종 앱을 표현 위한 연관성 있는 것들의 모임이라 할 수 있다.

- Diary에는 각각의 id가 있다.
- 제목에 대한 title 프로퍼티가 있다.
- 내용에 대한 diaryDetail 프로퍼티가 있다.
- diary내용을 구분지어주는 tag프로퍼티가 있다

Diary객체 안에는 메서드가 2개 있다.

- mutating func update()
- static func == (lhs: Self, rhs: Self) -> Bool { }
---------
## mutating func update()
struct안에 mutating 메서드를 구현해줌으로써 스스로 자기자신이 어떤 프로퍼티의 값을 바꾼다.

## Static func == (Equatable)
Diary에 대해서 업데이트를 할 때,
여러 개의 Diary 중에서 누구를 업데이트 시킬 것인가? 

즉, 각 구분자(id)를 구분해서 같은 것을 찾아서 업데이트를 시킨다.

Diary List 간에 동등비교를 하기 위한 메서드이다.

## Code
<img src="https://user-images.githubusercontent.com/88191880/165706417-b993cfa8-6893-4ac7-a10e-67a2a93658f2.png" width="500" height="180"/> 

--------

이제 Diary의 여러개들을 관리하는 DiaryManager를 만들어준다.
