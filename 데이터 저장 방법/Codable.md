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

이제 많은 Diary들을 관리하는 DiaryManager를 만들어준다.
view에서 정보들을 관리하는 viewmodel이 자기가 모르는 정보들을 DiaryManager에게 물어보고 가져온다.

## DiaryManager 구성

    1. DiaryManager 싱글톤 객체를 만들어준다.
    2. [Diary] 배열을 만들어준다.
    3. Diary 객체안에 프로퍼티 값을 업데이트 해주는 메서드를 생성한다.
    3-1. 새로운 Diary를 만들기 위해서는 next id를 넣어줘야 한다. (DiaryManager 인스턴스 변수 lastId에 +1한다.)
    4.add, delete, update 정보를 디스크에 "diary.json"이라는 파일명의 정보들을 저장하는 메서드를 만들어준다.
    그리고 add,delete,update할 때마다 디스크랑 싱크를 시켜준다.
    4-1. (delete)해당하는 diary id의 데이터를 삭제 해주기 위해 filter를 이용한다.
    4-2. (update)해당하는 diary를 찾아서 넘어오는 정보로 업데이트 시킨다.
    5. "diary.json"파일에 있는 내용들을 다 읽어와서 Diary객체로 전환 시켜주는 메서드를 만든다.

# Code
<img src="https://user-images.githubusercontent.com/88191880/166460241-1b2c4d93-2fd0-46da-9a17-5d6648ed644b.png" width="500" height="200"/> 

---------
## DiaryViewModel
view들을 구성하기 위해 필요한 정보들을 갖고 있는 viewmodel을 만들어준다. viewmodel은 manager에게 필요한 정보들을 얻는다.

    1. manager에서 Diarys배열을 가져와서 대입해준다.
    2. 메인화면에서 선택해서 들어간 박스의 tag값을 filter로 찾아 같은 데이터 값이라면 해당 데이터를 리턴해준다. 
    3. add,update,delete,loadTask 메서드는 manager에서 정보를 가져와서 구현해준다.

# Code
<img src="https://user-images.githubusercontent.com/88191880/166469164-eb7e5027-d68e-4441-966a-29afb0065e3c.png" width="500" height="160"/>

--------

## Struct -> json으로 인코딩해서 disk에 저장하기
