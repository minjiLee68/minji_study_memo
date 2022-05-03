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
<img src="https://user-images.githubusercontent.com/88191880/166460241-1b2c4d93-2fd0-46da-9a17-5d6648ed644b.png" width="500" height="300"/> 

---------
## DiaryViewModel
view들을 구성하기 위해 필요한 정보들을 갖고 있는 viewmodel을 만들어준다. viewmodel은 manager에게 필요한 정보들을 얻는다.

    1. manager에서 Diarys배열을 가져와서 대입해준다.
    2. 메인화면에서 선택해서 들어간 박스의 tag값을 filter로 찾아 같은 데이터 값이라면 해당 데이터를 리턴해준다. 
    3. add,update,delete,loadTask 메서드는 manager에서 정보를 가져와서 구현해준다.

# Code
<img src="https://user-images.githubusercontent.com/88191880/166469164-eb7e5027-d68e-4441-966a-29afb0065e3c.png" width="500" height="300"/>

--------

## Struct -> json으로 인코딩해서 disk에 저장하기

Diary struct 데이터들을 바로 json으로 쓴다.
(json은 Dictionary형태로 표현된다.)

json타입이 서버에서 내려오면 하나의 파일이 되기 떄문에, 데이터 형태로 만들 수 있다. (데이터를 swift가 알아먹을 수 있는 형태의 오브젝트로 만들어서 앱 내에서 사용하게 하는) 파싱을 해야 하는데, 그 과정이 손이 많이 가고, 시간이 많이 걸림 

But! Codable이 나오면서부터 파싱이라는 긴 과정을 할 필요가 없게된다. Codable은 json형태의 파일이 바로 Swift가 알아먹을 수 있는 struct형태로 바꿔준다.(오예)

이제 json 형태의 데이터들을 disk에 쓰고 읽을 수 있게 만들어준다.

    1.Diary 객체를 받아서 JSONEndocer를 통해 struct를 json타입으로 인코딩 시켜준다.
    2. 인코딩된 데이터를 가지고 FileManager형을 통해서 json데이터 타입을 실제 파일로 만들어줌
    2-1. FileManager.default.fileExists(at: ) -> 똑같은 이름이 있는지 확인
    2-2. FileManager.default.createFile -> json 데이터를 원하는 파일이름으로 써줌
    3. 파일이름을 통해 읽어와서 데이터 형태로 만들어주고 데이터형태를 JSONDecoder로 struct로 만들어준다.



# Code
<img src="https://user-images.githubusercontent.com/88191880/166490536-fea5cee1-2d94-44ca-8de5-47cd2001ff48.png" width="500" height="300"/>
