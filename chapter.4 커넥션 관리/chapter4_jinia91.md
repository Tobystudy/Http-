# TCP Connection

## 신뢰할수 있는 데이터 전송 통로
- 순서 보장
- 데이터를 세그먼트로 쪼개고 이를 IP패킷으로 담아 전송

![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/233770284269600?raw=true)

- 해당 세그먼트에 IP 헤더가 붙어서 IP 패킷이 됨

## 소켓프로그래밍의 인터페이스

- 기본적으로 C이지만, 자바, 코틀린도 네이티브 메서드를 사용하는 소켓프로그래밍이 존재하므로 알아둬서 나쁠게 없다.

> socket? 
> 소켓은 네트워크 상에서 돌아가는 두 개의 프로그램 간 양방향 통신의 하나의 엔드포인트. 
> https://popbox.tistory.com/66


- socket() : 연결이 되지 않은 익명의새로운 소켓 생성
- bind() : 소켓에 로컬 포트 번호와 인터페이스를 할당
- connect() : 로컬의 소켓과 원격의 호스트 . 및포트 사이에 TCP 커넥션 생성
- listen() : 커넥션을 받아들이기 위해 로컬 소켓에 허용함을 표시
- accept() : 누군가 로컬 포트에 커넥션을 맺기를 기다림
- read() : 소켓에서 버퍼에 n 바이트 읽기 시도
- write() : 소켓에서 버퍼에 n 바이트 쓰기 시도
- close() : Tcp 커넥션을 완전히 끊음
- shutdown() : TCP 커넥션의 입출력만 닫음

=> 고전적인 Blocking I/O 방식

## TCP 성능 지연

### 대표적인 원인들

- DNS 변환? : 캐시되어있지않거나 물리적으로 멀어 DNS 조회 자체가 오래걸리는경우, 하지만 현대 인프라에서 이정도 지연은 치명적이진 않다. 오히려 캐시되어있던 정보가 outdated 되어 장애가 일어나는경우가 더 문제가 많이 되는거같다.

- 커넥션 생성: TCP 핸드셰이크 지연
- 혼잡 제어를 위한 TCP Slow Start
- Nagle Algoritm
- TCP Piggyback ack 를 위한 확인 응답 지연 알고리즘
- Time_Wait 지연과 포트고갈


### TCP 커넥션 핸드셰이크 지연
- 기본적인 지연
- 커넥션을 재사용하지 않는 이상 어쩔수 없다

### 혼잡제어를 위한 Slow Start

- TCP 커넥션도 웜업이 필요하다!
- 각 RTT마다 cwnd 사이즈가 지수적으로 증가
- opening the congestion window(cwnd) 가 존재한다!
- 패킷 손실과같은 네트워크 혼잡 문제가 발생하면 선형적으로 감소시킴


#### BBR
- 구글에서 개발한 혼합 제어 알고리즘
- 리눅스 커널 4.9부터 탑재(2014~)
  - sysctl -w net.ipv4.tcp_congestion_control=bbr
  - 고대역폭 고지연 환경에서 유용하다함

### Nagle Algorithm - tcp NoDelay

- TCP/IP 통신시 데이터 청크 하나당 기본적인 오버헤드가 존재 : IP 헤더, TCP 헤더
- 만약 1바이트 단위로 끊어 보낸다고 가정하면 너무 많은 오버헤드가 존재할것
- 성능 최적화를 위해 기본적으로 패킷을 전송하기 전에 많은 양의 TCP 데이터를 한개의 덩어리로 합친다
  - congestion control in ip/tcp internetwork - nagele algoritm

- 다른 모든 패킷의 확인 응답을 받기 전까지 특정 임계점에 도달하지 못한 사이즈의 패킷은 전송을 지연하다가 임계점에 도달하거나, 다른 모든 패킷의 확인 응답을 받으면 그때 보냄으로서 네트워크 오버헤드를 줄인다.

#### CONS
-  문제는 작은 네트워크 응답을 요할때 해당 임계점에 도달하지못해 지연이 발생
- 확인 응답 지연 과 병행될경우 비효율적인 동작이 발생

> TCP_NODELAY 파라미터로 on / off 튜닝 가능

- 실시간성이 중요하다면 off 고려

### Piggyback Ack

- IP 프로토콜 자체는 패킷의 전송을 보장하지 않음(파기, 유실 등등)
- TCP는 성공적인 데이터 전송을 위해 확인 체계를 가짐
  - 무결성 체크섬
  - 순번

이떄 ACK이 너무 많이 날라가므로 이를 일반적인 다른 패킷과 묶어서 보내는 경향이 있음(PiggyBack)

이를 위해 많은 os 에서는 확인 응답 지연 알고리즘을 사용

- 송출할 확인 응답을 특정 시간동안 버퍼에 두고, 확인 응답을 편승시키기 위한 송출 데이터 패킷을 찾아 전송
- 문제는 HTTP의 경우 HTTP 요청, 응답자체가 하나의 청크이며 패킷단위로 잘게 쪼개지 않기때문에 http 통신 하에는 편승할만한 패킷들이 그리 많지않음
- 확인 응답 지연 알고리즘으로 인한 지연이 발생할 수 있음


#### solution?

- 지연시간 조절
- TCP_QUICKACK

### TIME_WAIT의 누적과 포트 고갈

- 커넥션이 끊겨도 TIME_WAIT 으로 빠지고 해당 커넥션에서 뒤늦게 도착할 수 있는 패킷의 혼선 문제를 막았음
- 잦은 커넥션 연결 및 해제가 필요한 서버의 경우 TIME_WAIT으로 인한 포트 고갈문제를 맞을 수 있음

#### Solution
- TIME_WAIT 조절, keepalive 고려


# HTTP Connection

## Http Connection Header

- 프록시, 캐시, 게이트웨이등 중개서버를 통과하는 경우 헤더가 유지되어야함
- 이때 Connection Header 값들이 전파될지 안될지 여부를 결정해야하며, Hop-by-Hop라는 개념을 통해 헤더 보호를 한다.

### Hop by Hop 헤더 특성

- Connection
- Keep-Alive
- Transfer-Encoding
- TE
- Upgrade
- Proxy-Authorization
- Proxy-Authenticate

## 성능향상을 위한 HTTP Connection 종류

### 병렬 커넥션
- 여러 tcp 연결을 통해 데이터 받기
- 대역폭의 한계가 있음
- 커넥션 핸들링문제와 총갯수 제한등으로 한계존재

### 지속 커넥션
#### Http 1.0+ 의 keep-alive

- http keep-alive 옵션

![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/233789679570976?raw=true)

![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/233795597283360?raw=true)

요청과 응답에 Connection: keep-alive / Keep-Alive: timeout 을 주고받아야한다.
만약 어느 한쪽이 없다면 keepAlive를 지원하지 않는것으로 인지

- 킵어라이브시엔 content-length가 정확해야함
- keep-alive를 인지하지 못하는 멍청한 프록시 문제를 조심해야함


#### 멍청한 프록시와 keep-alive

![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/233791000993824?raw=true)

(c), (e) 가 문제

프록시가 헤더는 전달했지만, keep-alive 동작은 이해하지 못하기때문에 응답을 받은 뒤, fin을 받기위해 마냥 기다리기만한다.

같은 커넥션으로 재요청이 오면 해당 요청은 새로운 핸드쉐이크를 하지 않은상태이며, 프록시는 이미 요청 받기를 다 했기때문에 추가적인 요청을 무시한다.

타임아웃이 날때까지 사실상 행이 걸림

- 홉별 헤더 원칙을 잘 준수하면 되지만, 멍청한 프록시는 이것도 안지켜서 문제

##### Solution

- proxy-Connection 헤더 사용하기
- 멍청한 프록시라면 넘어가도 서버가 알아먹지 못해서 괜찮고, 영리한 프록시라면 양방향 keepAlive가 걸리도록 connection: keep-alive를 할테니 문제가 없다.




#### http 1.1 은 디폴트가 지속 커넥션!

- 혹시나 몰라서 keep-alive 값을 Connection 헤더에 넣긴 하지만, 기본적으론 항상 지속 커넥션


#### TCP keep alive?

- 좀비 세션 해제를 위함이 더큼
- 연결 유지목적과는 약간 다르다

#### 지속 커넥션의 제한과 규칙

- Connection: close가 포함됬다면 해당 커넥션으로 추가적인 요청을 보낼수 없다
- content0length는 중요!
- 프록시는 서버 클라 각각에 keep-alive지속 커넥션을 맺어야한다
- 지속 커넥션이 끊길경우 커넥션 복구가 가능해야 하며, 페일오버로 재요청이 나가야한다
- 지속커넥션은 2개 초과는 자제하자

reference: https://blog.naver.com/whdgml1996/222153047879

### 파이프라인 커넥션
- 응답을 기다리지 않고 요청을 연속적으로 보내고 응답을 처리하는 방식



## 커넥션 끊기 룰?

### 마음대로 끊기
- 지속커넥션에서 서버가 마음대로 끊는 시점에 클라이언트의 요청이 유실될수도..?

### Content-Length , Truncation
### 커넥션 끊기의 허용, 재시도, 멱등성

- 커넥션이 끊기는 상황을 언제나 고려해야함
- 멱등성을 고려, 멱등한 요청에 대해서는 클라이언트는 재요청을 한다.
- 멱등하지 않은 요청의 경우는? 재시도 X

### 우아한 커넥션 끊기

- 애플리케이션은 TCP의 입출력 채널중 한쪽만 끊을 수 있다.
- 절반 끊기 사용!(shutdown())

![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/233801243525152?raw=true)

- 클라이언트 입장에서는 송신을 다하고 송신 채널을 닫으면됨
- 서버 입장에서는 응답 데이터를 모두 응답 후 송신 채널이 닫힘을 알고 서버도 송신 채널을 닫으면됨

- half close connection 관련인데 이부분 좀더 공부할 필요가 있다
