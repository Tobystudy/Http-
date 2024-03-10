# 메시지의 형태

- 요청 메시지
![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/233705361412128?raw=true)



        <메서드> <Url> <version>. <--- 시작줄
        <헤더>

        <본문>





- 응답 메시지

![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/233707270721568?raw=true)


        <버전> <상태코드> <사유> <--- 시작줄
        <헤더>

        <본문>



# CRLF

> Carrage Return Line Feed
>
> /r/n


# 시작줄 구성

## 메서드

- Http 명세에 정의된 공통 요청 메서드 집합

|메서드|설명|요청 메시지 본문이 있는가|
|-|-|-|
|GET|리소스 가져오기|X|
|HEAD|리소스에 대한 헤더만 가져오기|X|
|POST|처리해야할 데이터 보내기|O|
|PUT|리소스의 특정 내용을 보내기|O|
|TRACE|메시지가 프락시를 거쳐 서버에 도달하는 과정 추적|X|
|OPTIONS|서버가 어떤 메서드르 수행할 수 있는지 체크|X|
|DELETE|서버에서 문서를 제거|X|

### Safe Method?

https://developer.mozilla.org/en-US/docs/Glossary/Safe/HTTP

- 리소스에 변화를 주지 않는 단순 쿼리형 메서드를 의미
- 멱등성과는 또 다른 이야기
- GET, HEAD, OPTION

### idempotent

- 지정된 행위가 있을때 리소스의 상태가 항상 같은지 여부
- 같은 응답을 주느냐랑은 조금 다른 이야기다!
  - 예를 들어 특정 요청에 의해 새로운 리소스가 만들어지는경우, 동일 요청으로 동일한 응답이 오더라도, 새로운 리소스가 추가된 셈이므로 멱등성이 없다

멱등한 메서드
- GET, HEAD, DELETE, PUT, OPTION

멱등하지 않은 메서드
- POST, PATCH

### 멱등성 POST? PATCH?

- 두 메서드는 멱등하지 않게 구현해도 되지만, 멱등해도 된다
- 명세상 반드시 멱등하라는 말이 없을뿐

https://tech.kakaopay.com/post/msa-transaction/

- 비즈니스 요구사항에 따라 POST나 PATCH는 멱등하게 만들기도 한다.

### GET
- 가장 일반적인 조회

### HEAD
- GET과 동일하게 동작하되, 본문은 주지않고 헤더만 주는 방식 


![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/233719733436448?raw=true)


### PUT
- 서버의 리소스를 갱신 / 생성
- 일반적으로 클라이언트는 리소스의 정확한 위치, 식별을 할 수 있다.
- 멱등성을 전제로함

### POST
- 서버의 리소스를 생성
- 멱등성이 없는게 일반적

### TRACE

![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/233727463583776?raw=true)

- 클라이언트의 요청 헤더가 루프백으로 돌아오는것
- 디버깅 목적으로 어떤 중간레이어를 거쳤는지, 서버에는 어떻게 도착하는지를 확인하기 위함
- 일반적인 서버는 TRACE를 허용하지 않으며, 톰캣도 역시나 안됨


        @Configuration
        class WebConfig {

        @Bean
        fun tomcatCustomizer(): WebServerFactoryCustomizer<TomcatServletWebServerFactory> {
            return WebServerFactoryCustomizer { customizer: TomcatServletWebServerFactory ->
                customizer.addConnectorCustomizers(
                    TomcatConnectorCustomizer { connector: Connector ->
                        connector.allowTrace = true
                    })
                }
            }
        }

- 위와같이 trace allow를 해주어야한다
- Trace 를 받은 서버는 실제 행위를 수행 하지 않는다


### OPTIONS

![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/233731986374688?raw=true)

해당 url로 수행할 수 있는 메서드들이 헤더에 담긴다.

### DELETE
- 삭제


## 상태 코드
![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/233710436327456?raw=true)


### 100~ : 정보성 상태코드

#### 100 continue

- 일종의 Can/Execute 패턴
- 클라이언트는 Expect 헤더로 해당 요청이 받아들여질수 있는지 물어보고 서버는 Continue를 반환하면 실제 요청을 보내는 방식
- Continue를 반환할지 여부는 애플리케이션 레벨보다는 일반적으로 서버 레벨에서 판단
- 413(Too Large), 415(Unsupport media Type), 417(Expectation Failed:) 등으로 판단 

- 실제 클라이언트 사이드 구현의 경우는 expect 요청 후, 응답을 마냥 기다리지말고 그냥 엔티티를 보내는 구현을 권장

### 200~: 성공 상태 코드

#### 200 : OK
#### 201 : Created
- 리소스 생성 요청에 대한 응답
- 생성된 리소스의 Location이 포함되어야함
#### 202 : Accepted
- 요청은 받아들어졌으나 서버는 아직 어떤 행위도 안한 상태
#### 204 : No Content
- body에 아무거도 없을때

#### 206 : 부분성공
- 쓴적이 없는데... 일단 부분성공시

### 300~: 리다이렉션
- 브라우저는 기본적으로 리다이렉션을 고려하고 있으므로 문제없지만, 서버사이드 입장에서는 거의 쓸일이없음
- 클라 애플리케이션이나 서버 투 서버 통신입장에서 리다이렉션 핸들링을 기본적으로 고려하고 있지도 않고, 고려하고 있다고 해도 강타입 스키마를 쓰는 입장에서 호환되지 않을 확률이 너무 높다.

### 400~: 클라이언트 에러 상태 코드

- 클라이언트의 요청이 잘못되었을 때

#### 400 : Bad Request
- 범용 클라이언트 요청 에러

#### 401: Unauthorized
- 클라이언트에게 인증하라고 요구
- 인증되지 않은 클라이언트의 접근시

#### 403 : Forbidden
- 서버가 거정 이유를 숨기고 싶을때 사용
- 일반적으론 인가되지않는 경우

#### 404 : Not Found
- 리소스가 없을때

#### 405 : Method Not Allowed
- 사용가능한 메서드가 헤더에 포함되어야함

#### 408 : Request Timeout
- 서버사이드 입장에서 타임아웃으로 요청을 끊는경우

#### 409 : conflict
- 요청이 리소스에 대해 일으킬 수 있는 몇몇 충돌을 지칭하기 위해 사용
- 응답은 충돌에대한 설명이 본문에 포함되어야함

#### 413 : Reqeust Too Large
- 요청이 너무클때
#### 415 : Unsupported Media Type
- 미디어 타입 지원 안할때

### 500~ : 서버 사이드 에러
#### 500: Internal Server Error
- 범용에러
#### 501 : Not Implemented
- API는 뚫어놨지만 구현을 아직 덜했을떄

#### 502 : Bad Gateway
- 클라이언트의 요청이 게이트웨이나 프록시서버까지 갔지만 실제 서버가 없거나 예상한 응답이 오지않을경우

#### 503 : Service Unavailable
- 서버가 임시적으로 안될때
- RateLimiter 등으로 일시적 제한이 있을때 내려주면 좋음

#### 504 : Gateway Timeout
- 게이트웨이나 프록시에서 타임아웃이 난 케이스


## 버전 번호

- 소숫점이 아니라 메이저 마이너 구분이므로 1.12가 1.8보다 높다
- 응답시 버전번호는 응답 애플리케이션이 해당 버전까지 호환한다는 의미이지 응답 받는 애플리케이션과는 관계없음

# 헤더 구성
- Http 명세는 여러가지 형태의 헤더를 정의하고 있음
- 공통 헤더, 요청 헤더, 응답 헤더, Entity 헤더, 확장 헤더, 등이 있으며 확장 정의 가능


### 일반 헤더

- Connection : 클라이언트와 서버가 요청 응답 연결에 대한 옵션을 정할 수 있게해준다
- Date
- MIME-Version
- Trailer Chunked transfer : 메시지의 끝 부분에 위치한 헤더들의 목록을 나열
- Transfer-Encoding : 수신자에게 안전한 전송을 위해 메시지에 어떤 인코딩이 적용되었는지 말해준다
- Upgrade : 프로토콜 업그레이드
- via :어떤 중개자를 거쳤는지, Trace 메서드에서 유용하게사용

- Cache-COntrol : 메시지와 함께 캐시 지시자를 전달하기 위해 사용

### 요청 헤더
#### Accept 관련 헤더
요청하는 클라이언트가 선호하는 응답 방식을 명시
#### 요청 보안 헤더
- Authorization : 클라이언트가 서버에게 제공하는 인증 그 자체에 대한 정보
- Cookie

### 응답헤더
#### 엔티티 캐싱 헤더
- ETag :엔티티 태그
- Expires : 엔티티 유효 기간
- Last-Modified : 엔티티 마지막 변경 일시
