# 🌱 모든 개발자를 위한 HTTP 웹 기본 지식
출저 : [`인프런 김영한 - 모든 개발자를 위한 HTTP 웹 기본 지식`](https://inf.run/xRKw)

<br>

## 목차
- [인터넷 네트워크](#인터넷-네트워크) 
- [URL와 웹 브라우저 요청](#URL와-웹-브라우저-요청) 
- [HTTP](#HTTP) 
- [HTTP 메서드](#HTTP-메서드)  
- [HTTP 메서드 활용](#HTTP-메서드-활용)  
- [HTTP 상태코드](#HTTP-상태코드)   
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


  
## HTTP 기본
### 클라이언트 서버 구조
### Stateful, Stateless
### 비 연결성(connectionless)
### HTTP 메시지
  
## HTTP 메서드
### HTTP 메서드 - GET, POST
### HTTP 메서드 - PUT, PATCH, DELETE
### HTTP 메서드의 속성

## HTTP 메서드 활용 

## HTTP 상태코드
### 상태코드 
- 클라이언트가 보낸 **요청에 대한 처리상태**를 **응답**에서 알려주는 기능입니다.

  - 1xx (Informational): 요청이 수신되어 처리중 (거의 사용하지 않음)
  - 2xx (**Successful**): 요청 정상 처리
  - 3xx (**Redirection**): 요청을 완료하려면 추가 행동이 필요
  - 4xx (**Client Error**): 클라이언트 오류, 잘못된 문법등으로 서버가 요청을 수행할 수 없음
  - 5xx (**Server Error**): 서버 오류, 서버가 정상 요청을 처리하지 못함

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
  - 304 Not Modified : 클라이언트 로컬 PC에 저장된 캐시를 사용합니다. (캐시로 리다이렉트)
  - 307 Temporary Redirect : 요청 메서드와 본문 유지(POST메서드이면 리다이렉트도 POST유지)
  - 308 Permanent Redirect : 요청 메서드와 본문 유지(POST메서드이면 리다이렉트도 POST유지)

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


### 캐시 지시어
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

