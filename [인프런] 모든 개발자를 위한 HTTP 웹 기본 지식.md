# 🌱 모든 개발자를 위한 HTTP 웹 기본 지식
출저 : [`인프런 김영한 - 모든 개발자를 위한 HTTP 웹 기본 지식`](https://inf.run/xRKw)

<br>

## 목차
- [인터넷 네트워크](#인터넷-네트워크) 
- [URL와 웹 브라우저 요청](#URL와-웹-브라우저-요청) 
- [HTTP 기본](#HTTP) 
- [HTTP 메서드](#HTTP-메서드)  
- [HTTP 메서드 활용](#HTTP-메서드-활용)  
- [HTTP 상태코드](#HTTP-상태코드)   
- [HTTP 헤더1 - 일반 헤더](#HTTP-헤더1---일반-헤더) 
- [HTTP 헤더2 - 캐시와 조건부 요청](#HTTP-헤더2---캐시와-조건부-요청)

<br>

## 인터넷 네트워크
- 대부분 개발자는 HTTP 프로토콜 위에서 개발합니다.
- 결국 다 서버(노드)를 통해서 전달됩니다.


### IP (인터넷 프로토콜)
- 클라이언트에서 서버까지 인터넷망을 통해서 통신하기 위해 IP 주소(IP Address)를 이용해 접근합니다.
- 출발지/도착지 지정한 IP 주소(IP Address)로 데이터를 전달합니다.
- 데이터를 전달할 때는 패킷(Packet)단위로 통신합니다.
- 보통 1500byte 단위로 나눠서 보냅니다. 
- 순서 보장 안됩니다.


### TCP (전송 제어 프로토콜)
- TCP, Transmission Control Protocol
- 연결지향 TCP 3 way handshake (가상연결)
- 데이터 전달 보증
- 순서 보장
- 신뢰할 수 있는 프로토콜
- 현재 대부분 TCP 사용


#### 프로토콜 계층
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226247273-31716721-b005-4b5d-bb63-828fd8313b96.png">


#### Ethernet frame
- 랜 카드에 등록된 MAC 주소 (물리적인 주소)

#### 패킷
- 수화물(package) + 덩어리 합성어


#### TCP/IP 패킷이란
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226247339-84eccf04-a257-4b80-b80d-94305af1d055.png">

- 출발지/도착지 IP, PORT, 전송 데이터, 부가정보들 포함되어 있는 정보를 뜻합니다.


#### TCP 3 way handshake 란?
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226247134-deb4a0b3-3c25-425a-8a12-2f43d7f01c7f.png">

- connect 연결과정에서 요청, 수락, 전송 3번 주고 받습니다. (논리적으로 연결)
1. 클라이언트(출발지)에서 -> 서버(도착지)로 접속 요청 (SYN) 씬
2. 서버(도착지)에서 -> 클라이언트(출발지)로 요청 수락 (SYN + ACK) 씨넥
3. 클라이언트(출발지)에서 -> 서버(도착지)로 ACK 함께 데이터 전송 가능 (ACK) 



### UDP (사용자 데이터그램 프로토콜)
- UDP, User Datagram Protocol
- 기능이 거의 없음
- 연결지향 - TCP 3 way handshake X
- 데이터 전달 보증 X
- 순서 보장 X
- 데이터 전달 및 순서가 보장되지 않지만, 단순하고 빠름

• 정리
- IP와 거의 같다. +PORT +체크섬 정도만 추가
- 애플리케이션에서 추가 작업 필요

#### HTTP/3 Web 
- 보통 TCP 프로토콜을 Web,영상 등 90% 점유율롤 TCP 대다수 사용했습니다.
- 최근에 TCP 3 way handshake 단계를 줄여 최적화해보자 하여 HTTP/3 Web 경우는 UDP 프로토콜을 사용하고 있습니다.

### PORT
- 항구라는 뜻
- 같은 IP 내에서 프로세스(어플리케이션) 구분

• 0 ~ 65535 할당 가능
• 0 ~ 1023: 잘 알려진 포트, 사용하지 않는 것이 좋습니다.
• FTP - 20, 21
• TELNET - 23
• HTTP - 80
• HTTPS - 443


### DNS (도메인 네임 시스템)
- DNS, Domain Name System
- IP 주소를 쉽게 읽을 수 있는 도메인 이름의 디렉터리 입니다.
- 도메인 명을 IP 주소로 변환홥니다.
<br>


## URL와 웹 브라우저 요청 
### URL
- URI, Uniform Resource Identifier
- URL, URN 포함
- Uniform: 리소스 식별하는 통일된 방식
- Resource: 자원, URI로 식별할 수 있는 모든 것(제한 없음)
- Locator: 리소스가 있는 위치를 지정
- scheme://[userinfo@]host[:port][/path][?query][#fragment]
- https://www.google.com:443/search?q=hello&hl=ko


#### scheme
- 주로 프로토콜 사용
- 프로토콜: 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙
- 예) http, https, ftp 등등
- http는 80 포트, https는 443 포트를 주로 사용, 포트는 생략 가능
- https는 http에 보안 추가 (HTTP Secure)



#### query
- key=value 형태
- ?로 시작, &로 추가 가능 ?keyA=valueA&keyB=valueB
- query parameter, query string 등으로 불림, 웹서버에 제공하는 파라미터, 문자 형태



### 웹 브라우저 요청 흐름
1. DNS 조회
2. IP, PORT 정보 조회
3. HTTP 요청 메시지
- GET /search?q=hello&hl=ko HTTP/1.1 -> HTTP 버전
- Host: www.google.com

#### HTTP 요청 메시지 전송
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226266592-50561a47-14ac-43cd-94c0-57976e17db0d.png">
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226266771-27e2aae7-3fa1-4cbd-b688-ae0c7b8ea30d.png">
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226266972-51f3fffc-a928-48f7-b690-75c20ee5e9be.png">
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226267030-69d897af-219b-4d21-bf32-794464aafa17.png">
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226267092-c05f45c1-9627-4791-b604-8ab0cdebbc8a.png">
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226267185-2da70e4f-d8fe-4ee2-8f69-f95a55a4126e.png">
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226267225-5976218a-c31b-4a41-ae2a-c082dc1f44a3.png">

<br>

## HTTP 메서드

### HTTP 메서드의 속성
- 안전(Safe Methods)
- 멱등(Idempotent Methods)
- 캐시가능(Cacheable Methods)

#### 안전(Safe Methods)
- 호출해도 리소스를 변경하지 않는다.

#### 멱등(Idempotent)
- f(f(x)) = f(x)
- 한 번 호출하든 두 번 호출하든 100번 호출하든 결과가 똑같다.
- 멱등 메서드
	- GET : 한 번 조회하든, 두 번 조회하든 같은 결과가 조회된다. 
	- PUT : 결과를 대체 한다. 따라서 같은 요청을 여러번 해도 최종 결과는 같다. Ex) 파일
	- DELETE : 결과를 삭제한다. 같은 요청을 여러번 해도 삭제된 결과는 똑같다. 
	- POST : 멱등이 아니다. 두 번 호출하면 같은 결제가 중복해서 발생할 수 있다.
- 멱등은 외부 요인으로 중간에 리소스가 변경되는 것 까지 고려하지 않습니다.

> 동일한 요청을 한 번 보내는 것과 여러 번 연속으로 보내는 것과 같은 효과를 지니고, 서버의 상태도 동일하게 남을 때, 해당 HTTP 메서드가 멱등성을 가졌다고 말합니다.

## HTTP 상태코드
### 상태코드 
- 클라이언트가 보낸 **요청에 대한 처리상태**를 **응답**에서 알려주는 기능입니다.

  - 1xx (Informational): 요청이 수신되어 처리중 (거의 사용하지 않음)
  - 2xx (**Successful**): 요청 정상 처리
  - 3xx (**Redirection**): 요청을 완료하려면 추가 행동이 필요
  - 4xx (**Client Error**): 클라이언트 오류, 잘못된 문법등으로 서버가 요청을 수행할 수 없음
  - 5xx (**Server Error**): 서버 오류, 서버가 정상 요청을 처리하지 못함
 
### 대표적인 응답 코드
  - 200 Ok : 요청이 성공적으로 수행되었음
  - 201 Created : 요청이 성공적으로 수행되었고, 새로운 리소스가 생성되었음
  - 400 Bad Request : 요청 값이 잘못되어 요청에 실패했음
  - 403 Forbidden : 권한이 없어 요청에 실패했음
  - 404 Not Found : 요청 값으로 찾은 리소스가 없어 요청에 실패했음
  - 500 Internal Server Error : 서버 상에 문제가 있어 요청에 실패했음

### 2xx (Successful)
- 클라이언트의 요청을 성공적으로 처리합니다.

  - 200 OK : 요청 성공
  - 201 Created : 요청 성공해서 새로운 리소스가 생성됨
  - 202 Accepted : 요청이 **접수**되었으나 **처리가 완료되지 않았음.**
  - 204 No Content : 서버가 요청을 성공적으로 수행했지만, 응답 페이로드 본문에 보낼 데이터가 없음.
    ※ 페이로드(payload) : 사용에 있어서 전송되는 데이터 

#### 201 Created
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226808910-4073b0bf-283f-40cc-b22f-669530eedf11.png">

### 3xx (Redirection)
- 요청을 완료하기 위해 유저 에이전트(웹 브라우저)의 추가 조치 필요합니다.
- 영구 리다이렉션, 일시 리다이렉션, 특수 리다이렉션(결과대신 캐시사용) 3가지 종류가 있습니다.

  - 300 Multiple Choices : 안쓴다.
  - 301 Moved Permanently : 요청 메서드가 GET으로 변하고, 본문이 제거될 수도 있음(MAY)
  - **302 Found** : 요청 메서드가 GET으로 변하고, 본문이 제거될 수도 있음(MAY)
  - 303 See Other : 요청 메서드가 GET으로 변경
  - 304 Not Modified : 캐시목적으로 사용. 변경된 것이 없음. 클라이언트 로컬 PC에 저장된 캐시를 사용합니다. (캐시로 리다이렉트)
  - 307 Temporary Redirect : 리다이렉트시 요청 메서드와 본문 유지(요청 메서드를 변경하면 안된다. MUST NOT)
  - 308 Permanent Redirect : 리다이렉트시 요청 메서드와 본문 유지(처음 POST를 보내면 리다이렉트도 POST 유지)

#### PRG:Post/Redirect/Get
- 일시적인 리다이렉션으로 POST요청시 중복 방지를 위해 결과 화면을 GET메서드로 리다이렉트합니다.

<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/226810931-b4e41364-5722-4f6a-889a-c08489ae66a2.png">

#### 실무에서는 뭘 써야 하는 지?
- 307,303을 권장하지만 현실적으로 이미 많은 애플리케이션 라이브러리들이 **302**를 기본값으로 사용합니다.
- 처음 302 스펙의 의도는 HTTP 메서드를 유지였으나, 대부분 웹 브라우저들이 GET으로 일부는 다르게 동작.

### 4xx (Client Error)
- 클라이언트 오류
  
  - 400 Bad Request : 클라이언트의 잘못된 요청으로 서버가 요청 처리 불가
  - 401 Unauthorized : 클라이언트가 해당 리소스에 대한 인증이 필요함
    - 인증(Authentication): 본인이 누구인지 확인, (로그인)
    - 인가(Authorization): 권한부여 (ADMIN 권한처럼 특정 리소스에 접근할 수 있는 권한, 인증이 있어야 인가가 있음)
  - 403 Forbidden : 서버가 요청을 이해했지만 승인을 거부함 (인증 자격 증명은 있지만, 접근 권한 불충분)
  - 404 Not Found : 요청 리소스를 찾을 수 없음

### 5xx (Server Error)
- 서버 오류
  - 500 Internal Server Error : 서버 문제로 오류 발생
  - 503 Service Unavailable : 서비스 이용 불가

- 500에러는 심각한 에러로 웬만하면 500에러 처리를 만들면 안됩니다. 

## HTTP 헤더1 - 일반 헤더

### 인증
- Authorization : 클라이언트 인증 정보를 서버에 전달
	- 인증하는 여러가지 매커니즘이 있습니다.
	- 매커니즘 마다 들어가는 값이 다릅니다. (JWT, OAuth 등의 값)
- WWW-Authenticate : 리소스 접근시 필요한 인증 방법 정의
	- 401 Unauthorized 응답과 함께 WWW-Authenticate 사용해야합니다.
	- WWW-Authenticate: Newauth realm="apps", type=1, title="Login to \"apps\"", Basic realm="simple"
	- 위 정보를 확인해서 인증 정보를 만들어주라고 서버에서 클라이언트로 응답합니다. 

### 쿠키
- **HTTP 프로토콜**의 **특성**이자 **약점**을 `보완`하기 위해서 쿠키 또는 세션을 사용합니다.
- 기본적으로 HTTP 프로토콜 환경은 "connectionless, stateless"한 특성을 가지기 때문에 서버는 클라이언트가 누구인지 매번 확인해야합니다. 
- 이 특성을 보완하기 위해서 쿠키와 세션을 사용하게됩니다.

#### HTTP 프로토콜 특성
- HTTP는 무상태(Stateless) 프로토콜이다.
- 클라이언트와 서버가 요청과 응답을 주고 받으면 연결이 끊어진다. 
- 클라이언트가 다시 요청하면 서버는 이전 요청을 기억하지 못한다.
- 클라이언트와 서버는 서로 상태를 유지하지 않는다.
- 정리, 연결이 끊어지기 때문에 이전 상태를 유지하지 않습니다.

#### HTTP 헤더 쿠키 셋팅
- 쿠키는 모든 요청에 쿠키 정보를 **자동** 포함되도록 개발
- Set-Cookie: 서버에서 클라이언트로 쿠키 전달(응답)
- Cookie: 클라이언트가 서버에서 받은 쿠키를 저장하고, HTTP 요청시 서버로 전달

1. 클라이언트 요청에 대한 응답으로 서버에서 HTTP 헤더의 Set-Cookie속성에 데이터(정보)를 담아서 응답을 합니다.
2. 서버로부터 응답 받은 클라이언트는 웹 브라우저의 쿠키 저장소에 데이터를 저장합니다.
3. 그 이후 웹 브라우저가 요청을 할 때마다 쿠키 값을 조회해서 HTTP 헤더의 Cookie속성에 데이터(정보)를 담아서 요청을 보냅니다.

#### 세션 탄생
- 모든 요청의 쿠키안에 로그인 정보가 노출되는 게 위험하여 보안에도 취약합니다.
- 그래서 고유 Key를 서버에서 생성 후 DB에 저장해놓고 쿠키에 부여한 것이 Session 입니다.

#### 쿠키와 세션 
- 예) set-cookie: sessionId=abcde1234; expires=Sat, 26-Dec-2020 00:00:00 GMT; path=/; domain=.google.com; Secure
- 사용처
	- 사용자 로그인 세션 관리
	- 정보 트래킹
- 쿠키 정보는 항상 서버에 전송됨
	- 네트워크 트래픽 추가 유발
	- 최소한의 정보만 사용(세션 id, 인증 토큰)
- 서버에 전송하지 않고, 웹 브라우저 내부에 데이터를 저장하고 싶으면 웹 스토리지 (localStorage, sessionStorage) 참고
- 주의!
	- 보안에 민감한 데이터는 저장하면 안됨(주민번호, 신용카드 번호 등등)

#### 쿠키 생명주기
- Expires, max-age
- **영속 쿠키**: 만료 날짜를 입력하면 해당 날짜까지 유지
	- Set-Cookie: `expires`=Sat, 26-Dec-2020 04:39:21 GMT
		- 만료일이 되면 쿠키 삭제
	- Set-Cookie: `max-age`=3600 (3600초)
		- 이나 음수를 지정하면 쿠키 삭제
- **세션 쿠키**: 만료 날짜를 생략하면 브라우저 종료시 까지만 유지


#### 쿠키 도메인
- 예) domain=example.org
- 명시: 명시한 문서 기준 도메인 + 서브 도메인 포함
	- domain=example.org를 지정해서 쿠키 생성
	- example.org는 물론이고
	- dev.example.org도 쿠키 접근

- 생략: 현재 문서 기준 도메인만 적용
	- example.org 에서 쿠키를 생성하고 domain 지정을 생략
	- example.org 에서만 쿠키 접근
	- dev.example.org는 쿠키 미접근

#### 쿠키 경로
- 예) path=/home
	- 이 경로를 포함한 하위 경로 페이지만 쿠키 접근
	- 일반적으로 path=/ 루트로 지정
- 예)
	- path=/home 지정
	- /home -> 가능
	- /home/level1 -> 가능
	- /home/level1/level2 -> 가능
	- /hello -> 불가능

#### 쿠키 보안
- Secure
	- 쿠키는 http, https를 구분하지 않고 전송
	- Secure를 적용하면 https인 경우에만 전송
- HttpOnly
	- XSS 공격 방지
	- 자바스크립트에서 접근 불가(document.cookie)
	- HTTP 전송에만 사용
- SameSite
	- XSRF 공격 방지
	- 요청 도메인과 쿠키에 설정된 도메인이 같은 경우만 쿠키 전송

#### 쿠키와 세션 정리
- 쿠키와 세션은 비슷한 역할을 하며, 동작원리도 비슷합니다. 그 이유는 세션도 결국 쿠키를 사용하기 때문입니다.
- 가장 큰 차이점은 **사용자의 정보**의 `저장위치`와 `라이프 사이클` 입니다.
- 쿠키는 서버의 자원을 전혀 사용하지 않으며, 세션은 서버의 자원을 사용합니다.
- 보안 면에서는 세션이 더 우수하며, 요청 속도는 쿠키가 세션보다 더 빠릅니다. 그 이유는 세션은 서버의 처리가 필요하기 때문입니다.
- 라이프 사이클, 쿠키는 파일로 저장되기 때문에 브라우저를 종료해도 만료시간까지 계속해서 정보가 유지됩니다.
- 반면에 세션도 만료시간을 정할 수 있지만, 브라우저가 종료되면 만료시간 상관없이 삭제됩니다.
- 그러나, 세션은 사용자의 수 만큼 서버 메모리를 차지하기 때문에, 최근에는 문제점들을 보완한 토큰 기반의 인증방식을 사용하는 추세 입니다. (JWT, Oauth)


## HTTP 헤더2 - 캐시와 조건부 요청

### 캐시 무효화
#### 캐시 무효화 하는 이유?
- 중요한 데이터를 다루는 경우에는 캐시를 유지하면 안되기때문에 캐시를 무효화 시켜야 합니다. 
- 캐시를 무효화 시키는 설정을 하는 이유는 캐시를 적용안해도 웹브라우저에서 GET 요청일 경우 임의로 캐시를 하는 경우가 있습니다.
- 이러한 경우를 방지하기 위해서 명시적으로 캐시하지 않겠다고 표기하는 것 입니다.
- 또한, 본인도 모르는 프록시 서버가 추가로 생길 수도 있기 때문에 옵션을 넣어두는 것이 좋습니다.

#### 확실한 캐시 무효화 응답
- 해당 호출을 캐시하면 안된다. 항상 새로운 호출을 하라

	- Cache-Control: no-cache, no-store, must-revalidate
	- Pragma: no-cache // HTTP 1.0 하위 호환


ex) Java Servelt
response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate"); // HTTP 1.1.
response.setHeader("Pragma", "no-cache"); // HTTP 1.0.
response.setHeader("Expires", "0"); // Proxies.


#### 캐시 지시어
- Cache-Control: no-cache
	- 데이터는 캐시해도 되지만, 항상 원 서버에 검증하고 사용(이름에 주의!)

- Cache-Control: no-store
	- 데이터에 민감한 정보가 있으므로 저장하면 안됨(웹 브라우저 내부 메모리 공간에서 사용하고 최대한 빨리 삭제)

- Cache-Control: must-revalidate
	- 캐시 만료후 최초 조회시 원 서버에 검증해야함
	- 원 서버 접근 실패시 반드시 오류가 발생해야함 - 504(Gateway Timeout)
	- must-revalidate는 캐시 유효 시간이라면 캐시를 사용함

- Pragma: no-cache
	- HTTP 1.0 하위 호환

