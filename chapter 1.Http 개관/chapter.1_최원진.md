# Goal 
> Http의 핵심 기술을 파악하고 웹의 기초 이해하기

# Chapter 1. Http 개관

- 신뢰성 있는 통신 기술

## 리소스

### MIME Type
> multipurpose internet mail extentions 

객체가 어떤 타입인지 명시

Content-Type으로 명시됨

application/json


### URI

> uniform resource identifier, 통합 자원 식별자

- 웹 서버 리소스 구체적 이름

- 세부 구분으로 URL, URN이 있음 둘을 합쳐서 URI라고 한다.

#### URL(Uniform Resource Locator, 통합 자원 지시자)

- 구성
  - Scheme : 프로토콜 기술
  - 인터넷 주소(도메인)
  - 웹서버의 구체적 리소스(/customer/12345)

#### URN(Uniform Resource Name, 통합 자원 이름)

- 리소스만의 고유한 이름으로 프로토콜이 변경되더라도 같은 동작을 보장
- 인프라차원에서 아직 리소스 위치 분석 - 매핑 이 원할하지 않음
- 실무에서는 거의 사용되지 않으며, ISBN 정도가 있음

- 구성
  - The leading scheme : urn:
  - NameSpace Identifier : NID
  - NameSpace-specific string : NSS

#### URN URL 차이점?

- URN은 prefix가 항상 urn: , 기술이나 프로토콜에 네임 스페이스가 영향받지않음

> 통상적인 관례상, 실무상 URI == URL

## 트랜잭션
- 요청과 응답

### Http Method

- 모든 Http 요청 메시지는 한개의 메서드를 가짐
- GET, PUT, POST DELETE ...

### Http status code

## 메시지

- Http 메시지는 이진형태가 아닌 일반 텍스트 형태이며, 당연하게 본문에는 임의의 이진데이터를 담을수도 있음
- 요청 메시지 / 응답 메시지 두가지가 존재
- 구성
  - 시작줄
  - 헤더
  - 본문

 
## TCP 커넥션

> 신뢰성 있는 패킷 교환 네트워크 프로토콜의 집합

성질
- 오류없는 데이터 전송
- 순서에 맞는 전달
- 조각나지 않는 데이터 스트림


- 제 3 계층 네트워크 계층의 IP 프로토콜을 기반으로 해당 주소를 URL로 사용
- DNS 인프라를 이용해, 도메인  이름 또는 호스트 명으로 매핑도 가능

### 간단한 TCP /IP 프로토콜 통신 과정

1. 웹 브라우저는 서버의 URL에서 호스트 명을 추출한다.
2. 서버의 호스트명을 IP로 변환
3. URL에서 포트번호를 추출
4. 웹서버와 TCP 커넥션 맺기
5. 웹 브라우저는 서버에 HTTP 요청 보내기
6. 서버는 웹브라우저에 HTTP 응답
7. 커넥션이 닫히면 웹브라우저는 문서 렌더링

## HTTP 프로토콜 버전

### 0.9
- 91" 오직 GET만 지원

### 1.0
- 헤더, 추가 메서드, 멀티미디어 객체 처리 추가

### 1.0+
- http keep-alive
- 가상 호스팅 지원
- 프락시 연결 등 
- 규격 외의 확장된 HTTP 버전

### 1.1
- 기존 1.0의 구조적 결함 교정, 성능 최적화, 잘못된 기능 제거에 집중

### 2.0
15" 공식적으로 표준화, 구글의 SPDY 프로토콜 기반

[SPDY란?](https://d2.naver.com/helloworld/140351)

- 이진 프레이밍 계층 : 텍스트 기반이 아니므로 성능이 더 빠름
- 다중화 : 하나의 TCP 연결로 여러개의 메시지를 동시에 주고 받을수 있음
- 서버푸시 
- 헤더 압축
- 우선순위 지정


## 웹의 기타 다양한 구성요소들

- 프로시, 캐시, 에이전트, 게이트웨이, 터널 등등
