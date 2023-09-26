# \[1] 웹 프로토콜

- 웹(The Web, W3, WWW, WorldWideWeb) : 인터넷에 연결된 사용자들이 그림, 소리, 영상, 문자 등의 멀티미디어 정보를 쉽게 공유할 수 있는 범세계적 정보 공간
- 프로토콜(Protocol) :  컴퓨터 또는 전자기기 간의 원활한 정보 통신을 위해 약속한 통신 규약

## 1) HTTP(HyperText Transfer Protocol)

- 하이퍼텍스트(hypertext) : 하이퍼링크(hyperlink, 다른 문서에 대한 참조)를 통해 다른 문서로 연결될 수 있는 문서

> 하이퍼텍스트를 효율적으로 교환하기 위한 _애플리케이션 계층_ 통신 규약

즉, HTTP는 웹에서 클라이언트(브라우저)-서버 간의 효율적인 통신을 돕는 프로토콜이라고 할 수 있다.

## 2) TCP(Transmission Control Protocol), UDP(User Datagram Protocol)

기본적으로 TCP와 UDP는 모두 _전송 계층_ 통신 규약이다.

UDP는 TCP보다 빠른 속도와 적은 네트워크 부하를 보여준다는 장점이 있지만, 데이터의 연결성과 신뢰성을 보장하지 않아 연속성이 중요한 실시간 스트리밍과 같은 서비스에 사용된다.
반면 TCP는 연결성과 신뢰성을 보장하기 때문에 후술할 IP의 단점을 보완하여 오늘날 HTML/1.1과 함께 주로 사용되고 있다.

## 3) IP(Internet Protocol)

> 송신 호스트와 수신 호스트가 패킷을 교환할 때 사용하는 _인터넷 계층_ 통신 규약

- 비연결성 : 패킷을 받을 대상이 없거나 서비스 불능상태여도 패킷을 전송
- 비신뢰성 : 패킷이 손실되거나 순서대로 전달되지 않을 우려가 있음
- 프로그램 구분 불가 : 같은 IP를 사용하는 서버에서 통신하는 애플리케이션이 둘 이상이라면?

---
# \[2] HTTP

## 1) 특징

- 무연결성(Connectionless) : 서버는 클라이언트의 요청에 응답하고 나면 연결을 종료하여 과부하 방지
- 무상태성(Stateless) : 각 클라이언트의 각 요청들을 모두 독립적으로 받아들여 상태정보를 저장하지 않음

## 2) 동작 과정

1. 클라이언트-서버가 TCP 연결
2. 클라이언트가 서버에게 `요청(Request)`
3. 서버가 클라이언트에게 `응답(Response)`
4. TCP 해제

## 3) HTTP 메시지

### 1. 요청(Request)

```
POST / HTTP/1.1
Host: localhost:8000
User-Agent: Mozilla/5.0 (Macintosh;... )... Firefox/51.0
Accept: text/html,apllication/xhtml+xml,...,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Upgrade-Insecure-Requets: 1
Content-Type: multipart/form-data; boundary=-12656974
Content-Length: 345

-12656974
(more data...)
```

#### 💡 HTTP Method

- 요청의 목적 및 종류를 지시

|핵심 메소드|기능|
|:---:|:---:|
|GET|리소스 조회|
|POST|요청 데이터 처리. 주로 등록에 사용|
|PUT|리소스 대체|
|PATCH|리소스 부분 변경|
|DELETE|리소스 삭제|

|기타 메소드|기능|
|:---:|:---:|
|HEAD|GET과 동일하지만 Status-line과 Header만 조회|
|OPTIONS|대상 리소스에 대한 통신 가능 옵션(메서드)을 설명(주로 CORS에서 사용)|
|CONNECT|대상 자원으로 식별되는 서버에 대한 터널을 설정|
|TRACE|대상 리소스에 대한 경로를 따라 메시지 루프백 테스트를 수행|

### 2. 응답(Response)

```
HTTP/1.1 403 Forbidden
Server: Apache
Content-Type: text/html; charset=iso-8859-1
Date: Wed, 10 Aug 2016 09:23:25 GMT
Keep-Alive: timeout=5, max=1000
Connection: Keep-Alive
Age: 3464
Date: Wed, 10 Aug 2016 09:46:25 GMT
X-Cache-Info: cashing
Conetent-Length: 220

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML2.0//EN">
(more data...)
```

#### 💡 Status Code

- 요청에 대한 서버의 처리 결과를 지시

|코드 분류|의미|해석|
|:---:|:---:|:---:|
|1xx|Informational|요청이 수신되어 처리 중|
|2xx|Successful|요청 정상 처리|
|3xx|Redirection|요청 완료를 위해 추가 조치 필요|
|4xx|Client Error|잘못된 문법 등으로 요청 처리 불가|
|5xx|Server Error|유효한 요청을 처리 실패|

|핵심 코드|의미|해석|
|:---:|:---:|:---:|
|200|OK|요청을 정상적으로 처리함|
|201|Created|유효한 요청으로 새 리소스를 작성 완료함|
|204|No Content|리소스 변경/삭제 요청을 정상적으로 처리해 제공할 내용이 없음|
|400|Bad Request|잘못된 요청|
|401|Unauthorized|비인증 상태이므로 권한 없음|
|403|Forbidden|비인가 상태이므로 권한 없음|
|404|Not Found|요청된 리소스를 찾을 수 없음|
|500|Internal Server Error|서버 오류|
|502|Bad Gateway|게이트웨이 오류|

## 4) HTTPS (HTTP Secure)

### 1. 특징

> TLS(Transportation Layer Security) 인증서를 사용한 HTTP

HTTP는 암호화되지 않은 데이터를 전송하므로 이를 제3자가 가로챌 우려가 있다.
이에 또 다른 보안 계층을 추가하여 통신하고자 TLS 위에 HTTP를 얹은 것이 HTTPS이다.
즉, HTTPS는 HTTP에 요청/응답에 담긴 데이터들을 암호화할 수 있는 보안 기능을 추가한 것이다.
웹사이트에 개인 정보나 금융 정보와 같은 민감한 정보가 포함될 경우 특히 HTTPS의 중요성은 더 커진다.

참고로 HTTP는 80번 포트, HTTPS 443번 포트를 기본 포트로 사용한다.

### 2. 동작 과정

1. 클라이언트-서버가 서로 어떤 TLS 버전을 사용 가능한지 확인
2. 인증서를 통해 서로를 믿을 수 있는지 확인
3. 통신에 사용할 암호를 교환
4. 교환한 암호로 통신을 암호화하여 데이터를 제3자가 도청할 수 없게 함

***
# \[3] REST

💡 REST(Representational State Transfer)
: 네트워크 자원들을 적절히 사용하기 위한 소프트웨어 아키텍처 스타일

## 1) 구성 요소

|구분|특징|
|:---:|:---:|
|자원(Resource)|모든 자원은 서버에 존재하며, 고유 ID로 오직 하나의 URI를 갖게 된다.|
|행위(Verb)|클라이언트는 HTTP Method로 자원에 대한 조작을 요청할 수 있다.|
|표현(Representation)|서버는 요청에 해당하는 적절한 응답을 클라이언트에 보낸다.|

## 2) 원칙

### 1. Uniform Interface (인터페이스 일관성)

> 프로그래머가 당신의 API 중 하나에 익숙해졌다면 다른 API도 쉽게 활용할 수 있어야 한다.

자원을 조작하기 위한 인터페이스들은 한정적이고 일관되어야 한다.
자원들은 네이밍 컨벤션, 링크 포맷, 데이터 포맷 등에 있어 통일감을 유지해야 하며, HTTP GET처럼 사용자들로 하여금 간단하고 일관된 접근이 가능하도록 해야 한다.
그 결과 특정 언어나 플랫폼에 종속되지 않고 자원을 조작할 수 있게 된다.

### 2. Client-Server (클라이언트-서버 구조)

> 서버와 클라이언트는 상호독립적인 영역이어야 한다.

클라이언트는 오직 URI만을 알고 있는 상태로 사용자 인증이나 상태 정보 등을 직접 관리하고 서버는 API를 제공하는 구조로써 각각의 역할을 확실히 구분해야 한다.

### 3. Stateless (무상태성)

> 상태 정보는 서버가 아닌 클라이언트에서 관리해야 한다.

서버는 들어오는 요청만 단순히 처리함으로써 서비스의 자유도를 높일 수 있고, 불필요한 정보를 관리하지 않음으로써 구현이 단순해진다.

### 4. Cacheable (캐시 기능)

> 적절한 캐싱은 클라이언트-서버 간 통신 비용을 줄여 확장성 및 성능 향상에 도움을 준다.

HTTP에서 사용하는 태그들을 활용해 캐시를 처리함으로써 전체 응답시간, 성능, 서버 자원 이용률 등을 향상시켜 클라이언트와 서버 모두에게 만족감을 제공한다.

### 5. Layered System (계층화)

서버를 다중 계층으로 설계하여 각 서버별로 기능을 달리 할 수 있고, 이는 시스템 규모 확장성을 향상시킨다.
클라이언트는 대상 서버에 직접 연결되었는지, 또는 중간 서버를 거쳐 연결되었는지 알 수 없다.

### 6. Code on Demand, optional

서버는 클라이언트에게 자원을 보통 XML이나 JSON 형식으로 보여준다.
하지만 때에 따라 실행 가능한 코드를 전송해 사용자의 이해를 도울 수도 있다.

## 3) REST 인터페이스 원칙에 대한 가이드

- URI에는 자원을 표현하는 데 집중하며, 행위는 HTTP Method를 통해 표현한다.
- 자원을 표현할 때 컬렉션은 복수명사를, 도큐먼트는 단수명사를 사용한다.
- 파일 확장자는 URI에 포함시키지 않으며, Accept-Header를 통해 표현한다.

- 슬래시 구분자(/)는 계층 관계를 나타낼 때 사용하며, URI의 마지막 문자이면 안 된다.
- 가독성을 높일 때, 밑줄(\_)이 아닌 하이픈(-)을 사용한다.
- URI 경로에는 되도록 소문자만 사용한다.

## 4) 📖 URI 설계 과제

|API 기능|HTTP Method|URI|
|:---|:---|:---|
|이벤트 목록 조회|GET|/events|
|이벤트 조회|GET|/events/{id}||
|이벤트 등록|POST|/events|
|이벤트 수정|PUT|/events/{id}|
|이벤트 삭제|DELETE|/events/{id}|
|이벤트 상태 변경|PATCH|/events/{id}|
|특정 이벤트의 주문 목록 조회|GET|/events/{id}/orders|
|멤버 목록 조회|GET|/members|
|특정 멤버 권한 변경|PATCH|/members/{id}|
|특정 멤버 정보 조회|GET|/members/{id}|
|특정 멤버 정보 변경|PUT|/members/{id}|
|멤버 등록|POST|/members|
