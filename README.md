# TIL
## - 공부한 내용 자유롭게 정리하는 곳 👩‍💻
## - 공부 일기장 📝
----------------------
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

-----------------
# 🌤 오늘의 날씨
