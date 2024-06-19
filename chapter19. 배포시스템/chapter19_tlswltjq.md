# RPC
> 원격 프로시저 호출 (Remote Procedure Call)

분산 컴퓨팅 환경에서 `다른 주소공간`에서 `함수`혹은 `프로시저`를 실행할 수 있게하는 `프로세스간 통신 기술`<br>


# [gRPC](https://medium.com/naver-cloud-platform/nbp-%EA%B8%B0%EC%88%A0-%EA%B2%BD%ED%97%98-%EC%8B%9C%EB%8C%80%EC%9D%98-%ED%9D%90%EB%A6%84-grpc-%EA%B9%8A%EA%B2%8C-%ED%8C%8C%EA%B3%A0%EB%93%A4%EA%B8%B0-1-39e97cb3460)
## Server-Client Model
과거 하나의 메인프레임에서 동작하는 Monolithic구조로 설계됨

시간이 흐름에 따라 소형 컴퓨터 장비(PC, 워크스테이션 서버등)이 등장함에 따라 매우 고가인 메인 프레임워크를 비교적 저가의 워크스테이션 서버로 대체 하려함

그러나 초 고사양 서비스를 워크스테이션 서버에서 그대로 제공하기엔 무리가 있기에 메인 프레임워크의 기능을 워크스테이션 서버로 분산하고 네트워크를 통해 서비스를 제공하는 Server-Client Model 방식을 채택

## IPC(Inter Process Communication)
프로세스 : 상호 독립적임, 허나 필요에 따라 프로세스간 정보를 교환해야함

별도의 수단을 이용해 프로세스 통신하는 방법론을 통칭해 `IPC`라고 한다.
### IPC의 종류
공유 메모리, pipe, 메시지큐 등과 아래를 포함
1. Socket<br> :OSI7계층에서 4L, TCP또는 UDP를 사용하기위한 수단, 네트워크간 통신이라고 하기도하나 실질적으로는 로컬 컴퓨터 프로세스와 원격 컴퓨터의 프로세스가 IPC통신을 하는 것.<br> 많은 언어가 API의 형태로 제공중이지만 실제 통신 과정을 구현해야하기 때문에 장애처리가 어려워짐
2. RPC<br> :네트워크로 연결된 서버상의 프로시저를 원경으로 호출 할 수 있는 기능.<br>통신이나 call 방식을 신경쓰지 않고 사용할 수 있음. IDL(Interface Definication Language)기반으로 여러 언어를 가진 환경에서 확장이 쉬움<br>RPC의 핵심 개념 Stub
3. REST<br> :HTTP/1.1을 기반으로 URI를 통해 자원을 명시하고 HTTP메소드를 통해 처리하는 아키텍처.<br>REST는 일종의 스타일, 파라미터와 응답이 명시적이지 않다.<br>XML과 Json을 이용해 데이터 전달을 하는데 XML은 정의된 태그가 없어 높은 확정성을 가졌으나 복잡하고 비효율적인 구조로인해 느림, Json은 제공되는 데이터가 모두 String기반이기에 전송과 처리를 위해 Serialization이 필요하다는 점이 문제.
## gRPC(google Remote Procedure Call)
Protocol Buffer기반의 Serializer에  HTTP/2를 결합한 RPC프레임워크
### HTTP/2
기존의 HTTP/1.1은 메시지 전달마다 connection을 생성해야해 헤더가 중복되고 느림<br>HTTP/2에서는 하나의 connection으로 여러 메시지를 주고 받으며 헤더를 압축하여 전송하며 요청 없이도 리소스를 전달 할 수 있다.
### ProtoBuf(Protocol Buffer)프로토콜 버퍼
Protocol Buffer는 google에서 개발한 구조화된 데이터를 직렬화(Serialization)하는 기법

직렬화란 데이터 표현을 바이트 단위로 변환하는 작업

### ProtoFile
ProtoBuf는 주고 받는 데이터를 message로 정의
```proto
syntax = "proto3";

package simple;

message HelloRequest {
    string name = 1;
}

message HelloResponse {
    string message = 1;
}

service HelloService {
    rpc SayHello (HelloRequest) returns (HelloResponse);
}

```