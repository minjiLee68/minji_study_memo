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
<img src="https://user-images.githubusercontent.com/88191880/165247783-615a3186-e525-41f4-b3f5-6513be3bc89d.png" width="500" height="360"/>

----------------

    서버에서 응답받은 데이터를 파이차트에서 나타내도록 구현한다.
    먼저 메인 화면에서 보여주는 국내확진자 수와 국내 신규 확진자 수를 나타내도록 구현해준다. 그리고 정의한 메소드를 completion closure에서 sussess case문 에서 호출시켜준다.

## Code
<img src="https://user-images.githubusercontent.com/88191880/165327320-dfd129a7-e466-40df-81f3-3081687ad437.png" width="480" height="90"/>
<img src="https://user-images.githubusercontent.com/88191880/165328847-81a2db60-90a6-412c-ab87-af3d94ee595a.png" width="500" height="320"/>


    파이차트를 구성해주는 메서드를 만든다.
    covidOverViewList.compactMap으로 covidOverViewList 객체를 pieChartDataEntry 객체로 맵핑 시켜준다.
    그리고 pieChartDataEntry 객체로 맵핑 하기 위해서
    return pitChartDataEntry 인스턴스를 생성하고 value 파라미터에는 차트 항목에 들어가는 값을 넣어준다.
    그런데 시도별 신규 확진자 수가 차트의 값이 되게 만들어주어야 하는데,
    value 파라미터 타입이 double타입 이여서 string을 double 타입으로 바꿔주는 메서드를 만들어준다. 
    pieChart에 표시할 데이터 항목을 만들었으면, dataSet으로 항목을 묶어준다. (pieChartDataSet)
    이 메서드 또한 completion sussess안에 정의해준다.

## Code
<img src="https://user-images.githubusercontent.com/88191880/165329830-7c8bd4cf-9ebd-4b4a-af79-f43553362e48.png" width="500" height="400"/>
<img src="https://user-images.githubusercontent.com/88191880/165332268-57434e64-4f07-4625-bf08-62c1e9d103d8.png" width="500" height="90"/>

-------------

    해당 시도를 눌렀을 때 상세화면을 보여주는 화면 만들기
    covidOverView프로퍼티 생성 후 cell.label에 해당 데이터 대입해주기
    viewController에서 ChartViewDelegate를 채택하고 
    차트에서 항목을 선택하였을 때 호출이 되는 메서드인 chartValueSelected 메서드를 구현해주고 entry 메서드 파라미터에 의해 선택된 항목에 저장된 데이터를 가져와서 covidDetailViewController가 push되게 구현해준다.

## Code
<img src="https://user-images.githubusercontent.com/88191880/165447888-dbe2e062-8557-4f8d-addf-cce25dd57431.png" width="500" height="320"/>
<img src="https://user-images.githubusercontent.com/88191880/165448091-aa32ad93-9696-4c53-b4fc-d73d51fddeba.png" width="500" height="120"/>

----------------

    *최종 화면

<img src="https://user-images.githubusercontent.com/88191880/165448732-1247fb51-ffe5-4f1c-a3d1-132b40511df2.png" width="180" height="400"/> <img src="https://user-images.githubusercontent.com/88191880/165448837-fe785744-5bf6-4500-8671-693e7d5a5a4b.png" width="180" height="400"/>
