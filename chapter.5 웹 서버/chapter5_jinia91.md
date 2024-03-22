# 웹서버
## 정의 
- Http 요청을 받아 응답을 해주는 애플리케이션
- Http와 Tcp 제어 책임

## 웹 애플리케이션 서버와 무엇이 다른가
- 동적 컨텐츠의 제공 여부
![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/236199762452512?raw=true)

-  [웹서버 점유율](https://www.netcraft.com/blog/june-2023-web-server-survey/)![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/236199096381472?raw=true)

> 책에서 말하는 웹서버는 웹 애플리케이션 서버까지를 포함하고있는듯?

## 웹서버가 하는일 - 과정

### 1. 클라이언트 커넥션 수락

- 지속 커넥션이 있다면 사용, 그렇지 않다면 새 커넥션 열기

- 클라이언트 호스명 식별이 가능 : reverse DNS 를 이용해 hostname lookup으로 특정 호스트를 식별하고, 해당 호스트에 대해 특정 제어를 시도할 수 있다.
  - 스팸 필터링, 보안 목적으로 수행 가능

- ident 프로토콜 핸들링 : Ident 프로토콜은 인터넷 프로토콜 스위트의 일부로, RFC 1413에 정의되어 있으며, 네트워크 연결의 다른 끝에 있는 사용자의 아이덴티티를 조회하는 데 사용
  - 현대엔 잘 안 쓰임 몰라도 될듯
  - 느리고, 보안문제로 대부분의 방화벽에 막힘

### 2. 요청 메시지 수신

- 요청 메시지 파싱시 이해 가능한 수준의 분량 확보를 위해 네트워크로부터 읽어서 메시지 일부분을 메모리에 임시로 저장
  - 자바 소켓 프로그래밍 기준으로 커널 버퍼에 해당 - os 의 영역
  - 자바 Nio 채널 통신의 경우 해당 버퍼도 관리가 어느정도 가능 - directByteBuffer 로 native memory 제어 가능

- 일부웹서버는 요청 메시지를 쉽게 다룰수 있도록 내부 자료 구조에 저장

#### 커넥션 관리 아키텍처
https://github.com/jinia91/nio-study/blob/main/src/main/kotlin/_01_single_thread_oio_server/SingleSocketServer.kt

- 단일 스레드 - Blocking I/O
- 멀티 프로세스 - Blocking I/O
- 멀티 스레드 - Blocking I/O
- 다중 I/O (Multi flexing Nonblocking IO)
- 멀티 스레드 다중IO

### 3. 요청 처리 

### 4. 리소스의 매핑과 접근

- 웹서버는 docroot, DocumentRoot 로 매핑하여 리소스를 접근하며, 상대주소로의 오용을 막기위해 막는편
- 웹서버 기준 디렉토리로 요청을 받을경우, 에러 / 색인파일 / 디렉터리 그자체 등 다양하게 응답하는편
- 인증 역할도 일부 맡는다

- SSI(Server Side Includes)는 웹 서버에서 HTML 페이지를 클라이언트에게 전송하기 전에, 서버 측에서 페이지의 일부 내용을 동적으로 추가하거나 변경할 수 있는 방법을 제공합니다. SSI는 주로 웹 페이지에 반복적으로 포함되는 내용(예: 헤더, 푸터, 메뉴 등)을 별도의 파일로 관리하여, 여러 웹 페이지에서 재사용할 수 있게 해줍니다. 이를 통해 웹 페이지의 유지보수를 용이하게 하고, 중복 코딩을 줄일 수 있습니다.

### 5. 응답 만들기

- 타입 협상 (AbstractMappingContentNegotiationStrategy)

### 6. 응답 보내기
### 7. 로깅

## 자바 웹 애플리케이션 관점에서의 설명

- 웹서버 : 아파치
  - 역할 : 요청을 받아 매핑된 서블릿에게 요청 전달

- 웹 애플리케이션 서버 : 톰캣
  - 역할 : 서블릿 컨테이너, 서블릿의 최초 생성과 넘겨받은 요청에 대한 기본적인 핸들링.

- 웹 애플리케이션의 구체적 로직 : 스프링
  - 역할 : dispatcherServlet



