# 🌱 모든 개발자를 위한 HTTP 웹 기본 지식
출저 : [`인프런 김영한 - 모든 개발자를 위한 HTTP 웹 기본 지식`](https://inf.run/xRKw)

<br>

## 목차
- [인터넷 네트워크](#인터넷-네트워크) 
- [URL와 웹 브라우저 요청](#URL와-웹-브라우저-요청) 

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
1. 클라이언트(출발지)에서 -> 서버(도착지)로 접속 요청 (SYN)
2. 서버(도착지)에서 -> 클라이언트(출발지)로 요청 수락 (SYN + ACK)
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


