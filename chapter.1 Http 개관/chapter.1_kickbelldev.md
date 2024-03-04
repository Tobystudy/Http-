# 1장 HTTP 개관

## HTTP란?

> Hypertext Transfer Protocol

- 현대 인터넷의 공용어라고 할 수 있음.
- 신뢰성 있는 데이터 전송 프로토콜 기반(HTTP/3은 제외).
- 웹 클라이언트가 데이터를 요청/웹 서버가 데이터를 응답하는 구조.

## 리소스

- 웹 서버는 웹 리소스를 관리하고 제공하는 역할.
- 어떤 컨텐츠 소스도 리소스가 될 수 있음.

#### 정적 리소스

가장 간단한 리소스로, 웹 서버 파일 시스템의 정적 파일들.

#### 동적 리소스

사용자가 누구이고, 어떤 정보를 요청했는가 등에 따라 다른 컨텐츠를 생성하는 리소스.

### 미디어 타입 (MIME Type)

> Multipurpose Internet Mail Extensions Type

인터넷은 수많은 데이터 타입을 다룸. \
때문에 HTTP는 객체 각각에 MIME Type이라는 데이터 포맷 라벨을 붙임. \
이름에서 알 수 있듯 MIME Type은 원래 이메일에 사용하던 것인데, HTTP에도 채택.

`type/subtype;<parameter=value>` 구조를 따름. \
ex) `text/plain;charset=UTF-8`, `image/png`, `multipart/form-data`

### URI

> Uniform Resource Identifier

- 웹 리소스의 주소(이름), 리소스를 고유하게 식별 가능.
- URL과 URN으로 나뉨.
- 통상적인 관례로는 URI와 URL을 같은 의미로 봄.

#### URL

> https://www.example.com:8000/path/to/resource

scheme, hosts, path 등으로 이루어져 있음.

- scheme: 프로토콜 명시. `https://`, `ftp://`, `mailto:` 등등.
- hosts: 도메인, IP 등으로 주소를 나타냄.
- path: 해당 주소의 웹 서버에서 리소스가 위치한 경로.

#### URN

해당 리소스에 대해 영구적이고 유일무이한 이름. \
아직 실험중이고 널리 채택되지 않음.

`urn:<NID>:<NSS>` 형태로 표현됨.

### HTTP 트랜잭션

- HTTP는 요청과 응답으로 이루어져 있음.
- HTTP 메시지라고 불리는 정형화된 데이터를 통해 이루어짐.

### HTTP 메서드

- 서버가 어떤 동작을 취해야 하는지 알려주는 명령.
- 모든 HTTP 요청 메시지는 하나의 HTTP 메서드를 가짐.

### 상태 코드

- HTTP 응답 메시지는 상태 코드와 함께 반환됨.
- 세자리 숫자로 응답의 상태를 나타냄.

## HTTP 메시지

- 단순한 줄 단위의 일반 텍스트
- 클라이언트 -> 서버는 요청 메시지, 반대는 응답 메시지
- 시작줄, 헤더, 본문(바디)로 이루어져 있음.
  - 시작줄: 요청 메시지는 무엇을 해야 하는지/응답 메시지는 무엇이 일어났는지
  - 헤더: 콜론으로 구분된 `키: 값` 쌍으로 이루어진 데이터.
  - 본문: Optional하고 어떤 종류의 데이터도 들어갈 수 있음.

## TCP 커넥션

> Transmission Control Protocol

### TCP/IP

HTTP는 애플리케이션 계층 프로토콜. \
그 아래의 TCP/IP가 HTTP 메시지를 한 곳에서 다른 곳으로 옮겨줌.

#### TCP 특징

- 오류 없음
- 순서 보장
- 조각나지 않음

한 구절로 말하면 신뢰성 있는 프로토콜.

### 커넥션, IP, 포트번호

HTTP 메시지를 전송하기 전에 IP 주소와 포트번호를 사용해 클라이언트와 서버 사이에 커넥션을 맺음.

#### 웹 브라우저의 일반적인 HTTP 요청 과정

(HTTP 스트리밍 같은 기술은 제외)

1. URL에서 호스트를 추출.
2. 호스트가 도메인 네임이라면 IP로 변환.
3. 포트번호가 있다면 추출.
4. IP와 포트번호로 TCP 커넥션.
5. HTTP 요청 메시지 발송.
6. HTTP 응답 메시지 수신.
7. 커넥션 종료 후 리소스 노출.

## HTTP 버전

### HTTP/0.9

프로토타입으로, GET 메서드만 지원. \
멀티미디어 컨텐츠 MIME Type 지원X, HTTP 헤더, 버전 번호가 없는 등 결함이 많음.

### HTTP/1.0

0.9에서 빠져있던 멀티미디어 MIME Type, HTTP 헤더, 버전 번호 등을 추가한 버전.

### HTTP/1.0+

keep-alive 커넥션, 가상 호스팅, 프록시 연결 등 공식적이진 않지만 사실상의 표준으로 기능 추가된 버전을 HTTP/1.0+ 라고 부름.

### HTTP/1.1

HTTP의 구조적 결함 교정, 성능 최적화, 잘못된 기능 제거. 더 복잡해진 웹 애플리케이션과 배포를 지원.

### HTTP/2.0

구글의 SPDY 프로토콜 기반. 2015년 표준 제정.
[https://www.cloudflare.com/ko-kr/learning/performance/http2-vs-http1.1/](https://www.cloudflare.com/ko-kr/learning/performance/http2-vs-http1.1/)

### HTTP/3.0

구글의 QUIC 프로토콜 기반으로 한 새로운 표준 프로토콜. (2022년 제정)
[https://www.cloudflare.com/ko-kr/learning/performance/what-is-http3/](https://www.cloudflare.com/ko-kr/learning/performance/what-is-http3/)

## 웹의 구성요소

- 프록시: 클라이언트와 서버 사이에 위치한 HTTP 중개자
  - 대개 클라이언트의 요청을 수정한 뒤에 서버에 전달.
  - 주로 보안을 위해 사용.
  - 요청/응답을 필터링.
- 캐시: 많이 찾는 리소스를 클라이언트 가까이에 보관하는 HTTP 창고
  - 자주 요청되는 리소스의 사본을 저장해 두고 요청이 오면 반환해줌.
  - 물리적인 거리가 중요.
- 게이트웨이: 다른 애플리케이션과 연결된 특별한 서버
  - 서버인 동시에 클라이언트임.
  - 스스로가 리소스를 갖고 있는 진짜 서버인 것 처럼 요청을 다룸.
- 터널: HTTP 통신을 전달하기만 하는 특별한 프록시
- 에이전트: 자동화된 HTTP 요청을 만드는 semi-intelligent 클라이언트
