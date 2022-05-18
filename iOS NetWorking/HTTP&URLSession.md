## 🍎 HTTP / URLSession
## HTTP란?
    HTTP: Client에서 데이터가 필요할 때마다 Server에게 요청하고, Server는 그 데이터를 응답하고 바로 연결이 종료 되는 방식
HTTP통신은 단방향 통신이라, Client만 Server에게 요청할 수 있고, Server는 Client에게 요청할 수 없다!
    
    HTTP는 크게 Request와 Response로 구분된다. Client가 Request 메세지를 작성해서 Server에 보내면 Server는 Response를 보내준다.
Request와 Response Message는 각각 Header와 Body로 구분된다.

------------------

## HTTP Message 구조
HTTP 메시지는 크게
    
    요청 메시지
    응답 메시지

두 가지로 나눌 수 있다. 이 두 메세지 모두 (*라인 - *헤더 - *바디) 세 부분으로 구성 된다. 

### *라인
메시지의 가장 기본 내용인 응답/요청 여부, 메시지 전송 방식, 상태 등이 작성된다. 매우 정형화되어 있어서 무조건 한 줄로만 작성된다.

    Post /userAccount/login HTTP/1.1

보통 이런 식이다. 가장 처음에 POST/GET등의 전송 메서드를 정의하며 시작한다.뒤이어 요청 내용에 대한 경로가 오고, 마지막으로 요청 형식에 대한 버전 정보가 온다. 이렇게 한 줄이 바로 라인에 해당한다.

### *헤더
메시지 본문에 대한 메타 정보가 들어가는 부분으로, 필요한 만큼 여러 줄로 작성된다. 길이가 유동적이므로, 바디와 구분을 짓기 위해 한 줄의 공백이 삽입 된다.(HTTP 메시지에서 나타나는 첫 번째 한 줄의 공백이 헤더와 바디를 구분하는 경계선)

    Host: swiftapi.rubypaper.co.kr:2029.
    Content-Type: application/json

Host란 Key에는, 도메인 및 포트번호가 Value로 오고, Content-Type이란 Key에는 메시지 바디의 타입을 나타낸다.

### *바디
실제로 보내고자 하는 메시지 본문 내용이 들어가는 부분으로, 길이가 유동적이다.
바디에 들어가는 메시지 형식은, 헤더의 Content-Type에서 설정한 Type과 일치해야 한다. JSON으로 설정했다면

    { "name" : "Minji" }

이런 식으로 가야 한다.

---------------

## HTTP Method
라인 첫번 째에 전송 메서드를 쓴다.
http method는 9가지가 있다.
(내가 배운바 로는...)

    - Get: 서버로 부터 데이터를 취득
    - POST: 서버에 데이터를 추가, 작성
    - PUT: 서버의 데이터를 갱신, 작성
    - PATCH: 리소스의 일부분을 수정
    - DELETE: 서버의 데이터를 삭제
    - HEAD: 서버 리소스의 헤더 (메타 데이터의 취득)
    - OPTIONS: 클라이언트가 서버에서 해당 URL이 어떤 메소드를 지원하는지 확인 할 때 사용
    - CONNECT: 클라이언트가 프록시를 통하여 서버와 SSL 통신을 하고자 할 때 사용
    - TRACE: 클라이언트와 서버간 통신 관리 및 디버깅을 할 때 사용

이렇게나 많지만..GET,POST,PUT,DELETE가 대표적이다. (CRUD)

    조회: GET
    등록: POST
    수정: PUT
    삭제: DELETE

## 1.GET
GET 메소드는 주로 데이터를 읽거나 검색할 때에 사용되는 메소드이다.

만약 GET요청이 성공적으로 이루어진다면 XML이나 JSON과 함께 200(ok) 응답코드를 리턴한다.

에러가 발생하면 주로 404(Not found)에러나 400(Bad request) 에러가 발생한다.

-   HTTP 명세에 의하면 GET 요청은 오로지 데이터를 읽을 때만 사용되고 수정할 때는 사용하지 않는다.
- GET 요청은 idempotent하다

    - idempotent : 멱등성이라고 하는데(처음 들어보는...)여러번 수행해도 결과가 같음을 의미한다고 한다. 즉, 호출로 인해 데이터가 변형이 되지 않는다는 말

-   같은 요청을 여러 번 하더라도 변함없이 항상 같은 응답을 받을 수 있다.
- 데이터를 변경하는 연산에 사용하면 안된다.

예시
    
    GET /user/1

데이터를 조회하는 것이기 때문에 요청시에 Body 값과 Content-Type이 비워져있다. 

조회할 데이터에 대한 정보는 URL을 통해서 파라미터를 받고 있는 모습을 볼 수 있다.

데이터 조회에 성공한다면 Body 값에 데이터 값을 저장하여 성공 응답을 보낸다.

GET은 캐싱이 가능하여 같은 데이터를 한번 더 조회할 경우에 저장한 값을 사용하여 조회속도가 빨라진다.

## 2.POST 
POST 메소드는 주로 새로운 리소스를 생성할 떄 사용된다.

조금 더 구체적으로 POST는 하위 리소스들을 생성하는데 사용된다. 

성공적으로 creation을 완료하면 201 응답을 반환한다.

- POST 요청은 idempotent 하지 않다.
- 같은 POST 요청을 반복해서 했을 때 항상 같은 결과물이 나오는 것을 보장하지 않는다.
- 두 개의 같은 POST 요청을 보내면 같은 정보를 담은 두 개의 다른 resource를 반환 할 가능성이 높다.

예시
    
    POST /user
    body : {date : "example"}
    Content-Type : "application/json"

데이터를 생성하는 것이기 때문에 요쳥시에 Body 값과 Content-Type 값을 작성해야 한다. 해당 예시는 JSON을 통해서 작성된 예시이다.

URL을 통해서 데이터를 받지 않고, Body 값을 통해서 받는다. 

데이터 조회에 성공한다면 Body 값에 저장한 데이터 값을 저장하여 성공 응답을 보낸다.

---------

## 3.PUT
PUT는 리소스를 생성 / 업데이트하기 위해 서버로 데이터를 보내는 데 사용된다.

- PUT요청은 idempotent 한다.
- 동일한 PUT 요청을 여러 번 호출하면 항상 동일한 결과가 생성된다.

예시

    PUT /user/1
    body: {date : "update example"}
    Content-Type : "application/json"

데이터를 수정하는 것이기 때문에 요청시에 Body 값과 Content-Type 값을 작성해야한다. 해당 예시는 JSON을 통해서 작성된 예시이다.

URL을 통해서 어떠한 데이터를 수정할지 파라미터를 받는다. 그리고 수정할 데이터 값을 Body값을 통해서 받는다.

데이터 조회에 성공한다면 Body 값에 저장한 데이터 값을 저장하여 성공 응답을 보낸다.

## 4. DELETE
DELETE 메서드는 지정된 리소스를 삭제합니다.

예시

    DELETE / user/1

데이터를 삭제하는 것이기 때문에 요청시에 Body 값과 Content-Type값이 비워져 있다. 

URL을 통해서 어떠한 데이터를 삭제할지 파라미터를 받는다.

데이터 삭제에 성공한다면 Body 값 없이 성공 응답만 보낸다.

-----------------

## GET과 POST의 차이

    Get은 가져온다는 개념이고, Post는 수행한다는 개념으로 받아들이면 쉽다!

GET은 서버에서 어떤 데이터를 가져와서 보여줄 때 사용한다. 어떤 값이나 내용, 상태등을 바꾸지 않는 경우에 사용을 하는 것이다.

이에 비해, POST는 서버상의 데이터 값이나 상태를 바꾸기 위해서 사용한다.

게시판으로 예를 든 설명을 보았는데, 이렇게 알려주셨다.

    글의 내용에 대한 목록을 보여주는 경우나, 글의 내용을 보는 경우는 GET에 해당하고, 글의 내용을 저장하고, 수정할 때에는 POST를 사용하는 것이다.

------------

## URLSession을 이용한 HTTP통신
URLSession: 특정한 url을 이용하여 데이터를 다운로드하고 업로드하기 위한 api

기본적으로 request, response 구조를 가지고 있다.

URLSession 사용 순서
- Session Configuration을 결정하고, Session을 생성
- 통신할 URL과 Request 객체를 설정
- 사용할 Task를 결정하고 그에 맞는 Completion Handler 나 Delegate 메소드들을 작성
- 해당 Task 를 실행
- Task 완료 후 Completion Handler 클로저가 호출이 됨

URLSessionConfiguration
- URLSession을 생성하기 위해서 필요한 요소
- Configuration을 생성할 때는 URLSession 정책에 따라서 default, ephemeral, background 세 가지 형태로 생성

    - Default: 기본 통신으로 디스크 기반 캐싱을 지원한다.
    - Ephemeral: 쿠키나 캐시를 저장하지 않는 정책을 가져갈 떄 사용한다. (ex.Safari의 개인정보 보호 모드)
    - Background: 앱이 백그라운드에 있는 상황에서 컨텐츠 다운로드, 업로드를 할 때 사용한다.
    - 앱이 종료되어도 통신이 이뤄지는 것을 지원하는 세션

URLSession Delegate
- 네트워크 중간 과정을 확인할 수 있다.필요에 따라서 사용된다.

URLSession Task

작업에 따라서 3가지로 나뉜다.

- DataTask
    - Data를 받는 작업, Response 데이터를 메모리 상에서 처리하게 된다.
    - 백그라운드 세션에 대한 지원이 되지 않는다.
    - URL 요청을 실시하고 완료 시 핸들러를 호출하는 Task 형식
    - Task가 실행된 후 핸들러가 실행되기 때문에 탈출 Closure 형태로 받아와야 한다.

- UploadTask
    - 파일을 업로드 할 때 사용

- DownloadTask
    - 파일을 다운로드 받아서 디스크에 쓸 때 사용


어느정도 http와 urlsession에 대해서 이해했다. 이제 urlsession을 이용해서 http 통신을 받아오는 작은 어플(?)을 만들자!

<!-- -----------------
# 🌤 오늘의 날씨
날씨 오픈api를 사용하여 도시를 입력하면 도시별 현재 날씨에 대해서 보여주는 작은어플(어플이라기도 뭐한..)을 만들어보자

----------
먼저 WeatherInformation이라는 구조체를 생성해준다. 그 다음 Codable프로토콜을 채택해준다.

Codable을 채택했다는 것을 한 마디로,
weatherInformation 객체를 json 형태로 만들 수 있고, json형태를 weatherInformation 객체로 만들 수 있다.

서버에서 전달받은 날씨정보 json 데이터를 weatherInformation struct 타입으로 변환하는 작업, 즉 디코딩하는 작업을 해준다.

    - *Codable은 자신을 변환하거나 외부표현으로 변환하는 것을 의미
    * 외부표현이란? - json과 같은 타입
    - Codable은 DeCodable과 Encodable프로토콜을 준수하는 타입
    - DeCodable은 자신을 외부표현에서 Decoding할 수 있는 타입
    - EnCodable은 자신을 외부표현에서 Encoding할 수 있는 타입


## Code
<img src="https://user-images.githubusercontent.com/88191880/164479340-db07b595-b16b-40a4-80ca-73b11b121225.png" width="240" height="360"/>

json 형태의 데이터로 변환하고자 한다면 기본적으로 json 형태의 키와 사용자가 정의한 프로퍼티의 이름과 타입이 일치해야 한다.

만약에 키와 프로퍼티의 이름을 다르게 사용하고 싶다면 타입내부에서 codingkeys라는 string타입의 열거형을 선언하고 codingkey 프로토콜을 준수하게 만들어야 한다.

이제 viewController에서 urlsession을 이용해서 current weather api를 호출해서 도시의날씨를 가져온다.

## Code
<img src="https://user-images.githubusercontent.com/88191880/164479067-66497d97-ea41-4fea-ad3f-0fdae332ede1.png" width="600" height="300"/>

data task가 api를 호출하고 서버에서 응답이 오면 completion handler closure이 호출이 된다. closure안에 data 파라미터에는 서버에서 응답받은 데이터가 전달된다.reponse 파라미터에는 http 헤어 및 상태코드와 같은 응답 메서드 전달

JSONDecoder 인스턴스 생성 -> json 객체에서 데이터 유형의 인스턴스로 디코딩 하는 객체
한 마디로 Decoderble 또는 Codable 프로토콜을 준수하는 사용자 정의 타입으로 변환시켜준다.
decoder.decode 첫번째 파라미터에는 json을 맵핑 시켜 줄 codable 프로토콜을 준수하는 사용자 정의 타입을 넣어준다. from 파라미터에는 json에서 응답받은 data를 넣어준다.

이제 도시이름과 날씨가져오기 버튼을 누르면 CurrentWeatherAPI에서 입력한 도시의 현재 날씨 정보에 대해 요청할 수 있게 한다.

## Code
<img src="https://user-images.githubusercontent.com/88191880/164482555-534fa378-3039-4071-8874-d0652ddd6faf.png" width="400" height="120"/>

마지막으로 weatherInformation 객체를 전달 받을 수 있는 메서드를 만들어준다.
그리고 ui로 나타날 label text에 weatherInformation에서 맵핑된 name을 대입해주어서 도시이름이 표시되게 해준다.
weather 배열의 첫번쨰 요소가 상수에 대입되게 하고, 현재 날씨 정보가 표시되게 한다.
현재온도는 -> 현재 절대온도값에서 - 273.15를 빼줘서 섭씨온도로 변환시켜준다.

## Code
<img src="https://user-images.githubusercontent.com/88191880/164484071-4c77e740-ecf9-42f4-94e0-bf06c0e23c9c.png" width="500" height="260"/>


    최종 어플
    * 도시 입력 전          * 도시 입력 후
<img src="https://user-images.githubusercontent.com/88191880/164455592-368b6552-17f4-4ad1-9315-6939b9953766.png" width="160" height="320"/> <img src="https://user-images.githubusercontent.com/88191880/164455961-e620d0a7-1b53-4308-8a16-4be70e97a548.png" width="160" height="320"/> 

------------------

# 코로나 현황판 🦠
시도별 코로나 현황을 호출해주는 굿바이 코로나 api를 호출하여서 서버에서 응답받은 데이터를 가지고 차트를 보여주는 화면 구현 후 시도를 클릭하면 더 자세한 내용을 보여주는 화면까지 구현해준다.

    기능 상세
    - 시도별 신규 확진자 수가 파이 차트로 표시 되어야 한다.
    - 도시 항목을 선택하면 상세화면을 볼 수 있는 화면으로 이동되어야 한다.

    활용 기술
    - 굿바이 코로나 api 
    - Alamofire
    - Cocoapods

## Alamofire
Alamofire는 Swift 기반의 HTTP 네트워킹 라이브러리이다.
URL Session을 기반으로한 라이브러리로, 네트워킹을 단순화하고 네트워킹을 위한 다양한 메서드와 JSON 파싱등을 제공한다.

    - Alamofire 기능
    - 연결가능한(Chainable) Request/Response 메서드
    - URL / JSON / plist 파라미터 인코딩
    - 파일 / 데이타 / 스트림 / 멀티파트 폼 데이타 업로드
    - Request 또는 Resume 데이터를 활용한 다운로드
    - NSURLCredential을 통한 인증(Authentication)
    - HTTP 리스폰스 검증(Validation)
    - TLS 인증서와 공개 키 Pinning
    - 진행 상태 클로저와 NSProgress
    - CURL 디버깅 출력
    - 광범위한 단위 테스트 보장
    - 완벽한 문서화

## URL Session대신 Alamofire을 쓰는 이유?
- 코드의 간소화, 가독성 측면에서 도움을 주고 여러 기능을 직접 구축하지 않아도 쉽게 사용 가능

예를 들어 어떤 api를 get방식 호출하는 코드를 작성한다 했을 때
url session을 이용하여 코드를 작성하면, url 생성과 statusCode 검증 에러처리 등 많은 코드를 작성해야 하지만, alamofire을 사용하면 URL Session보다 적은 코드로 똑같은 기능구현이 가능하다.

url session은 호출할 api의 url의 api를 호출하고, query 파라미터가 있다면 url에 맵핑 시켜주는 코드를 작성해줘야 하지만,

alamofire는 요청을 생성할 때, method 파라미터의 url과 파라미터를 넘겨주면, 내부에서 자동으로 url 파라미터를 맵핑시켜준다.

유효성 검사같은 경우에도 url session은 reponse 객체를 HTTP Response 로 다운 캐스팅하여 statusCode 프로퍼티에 접근해 200번대인지 확인하는 코드를 직접 작성해주었지만, alamofire는 delegate 메서드만 호출을 하면 정상 statusCode 200번대만 허용하게 만들어준다.

------------

    먼저 응답받은 json data를 맵핑할 수 있는 구조체를 만들어준다.
    하나의 객체 안에 시도명 키를 가지고 있는 객체를 구조체로 정의해준다.(CovidOverView)
    그리고 시도명 키를 가지고 있는 객체를 맵핑하는 구조체를 정의해준다. (CityCovidOverView)
    CityCovidOverView 구조체 안에서는 시도명 객체를 맵핑하기 위해 프로퍼티를 선언해준다.

## Code
<img src="https://user-images.githubusercontent.com/88191880/165241679-688a0b60-d170-4b16-b526-f71f95d2d1cd.png" width="300" height="380"/>


    이제 alamofire을 이용해서 시도별 코로나 현황 api를 호출해준다.
    fetchCovidOverView라는 메서드를 만들어주고 메서드 파라미터에는 completionHandle를 전달받게 정의를 해준다.
    api를 요청하고 서버에서 json데이터를 응답 받거나 요청에 실패했을 떄
    completionHanler Closure를 호출해 해당 closure를 정의하는 곳에 응답받은 데이터를 전달한다.
    그리고 closure를 @escaping closure로 선언을 해준다. escaping closure는
    closure가 함수로 (escape) 탈출한다는 의미이다. 함수의 인자로 closure가 전달되지만,
    함수가 반환되는 후에도 실행되는 것을 의미한다. 함수의 인자가 함수의 영역을 탈출하여 함수 밖애서도 사용할 수 있는 개념
    @escaping closure를 사용하는 대표적인 예시로는,
    비동기 작업을 하는 경우이다.

## Code
<img src="https://user-images.githubusercontent.com/88191880/165245293-74072b11-895c-4727-a22b-d5894afc86f6.png" width="500" height="360"/>

-------------

    viewDidLoad에서 self.fetchCovidOverView 메서드를 호출해서 앱이 실행되고,
    viewController가 표시될 때, 시도별 코로나 현황 api를 호출하게 만들어준다.
    순환 참조를 방지하기 위해서 closure 헤드에 [weak self] 캡쳐 리스트를 표시해준다.
    guard let self = self else { return }구문을 작성해서 일시적으로 self가
    strong reference가 되게 만들어준다.

## Code
<img src="https://user-images.githubusercontent.com/88191880/165247783-615a3186-e525-41f4-b3f5-6513be3bc89d.png" width="500" height="360"/> -->
