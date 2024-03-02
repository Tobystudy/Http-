# URL 

- 리소스의 위치를 명시

## 구조

![uploaded image](https://github.com/jinia91/blogBackUp/blob/main/img/230989103906848?raw=true)

1. Scheme : 어떻게 리소스에 접근할것인지, 기술적인 프로토콜 명시
2. UserInfo : 주로 FTP 같은 특정 프로토콜에서 유저 인증을 위해 사용하던 방식
3. HostName : 리소스가 호스팅된 서버 위치로 도메인이나 ip 주소로 명시
4. Port 
5. Path : 리소스가 서버의 어디에 위치해있는지, 기본적으로 파일시스템과 유사
6. Query : 파라미터를 명시하는 곳으로 세미콜론 방식과 ?, & 을 사용하는 방식이 존재
7. fragment : 리소스의 특정 객체 지칭, 일반적으로 리소스로 나뉘는 단위를 표현하진 않음

### 세미콜론 vs 쿼리 파라미터

- [W3C Recommendation from 1999](https://www.w3.org/TR/1999/REC-html401-19991224/appendix/notes.html#h-B.2.2)
- [W3C Recommendation from 2014](https://www.w3.org/TR/2014/REC-html5-20141028/forms.html#url-encoded-form-data)
- [semicolon as url query seperator](https://stackoverflow.com/questions/3481664/semicolon-as-url-query-separator)

기존 레거시 url 표현법으로 세미콜론을 사용해 파라미터를 넘기기도 했으나 명확한 정의가 있지는 않음.

- 과거엔 & 인코딩의 불편함때문에 세미콜론의 사용도 허용했던 의도로 보인다.

## 안전하지 않은 문자

### URL 문자 집합

- 기본적으로 ASCII 인코딩이므로 여기에 존재하지 않는 문자에 대한 이스케이프 문자가 혀용됨
- %로 시작

### 예약어 제한
- /, %, # 등등

- 간혹 이스케이프 문자를 사용하지 않고 기입하기도 하는데 이는 안전하지 않은 방법이며, 이스케이프를 추천


## Scheme
### http
- 사용자 정보가 없다는 점을 제외하곤 일반 url 포멧을 지키는 http스킴, 포트는 디폴트 80

### https
- ssl
- 기본 포트는 443

### malito
- 이메일 주소
- malito: rfc-822
- 일반 url과는 많이다르다

### ftp
- 파일 전송 프로토콜(file transfer protocol)
- 웹과 url 보다도 역사가 오래된 프로토콜


### rtsp, rtspu

- 실시간 스트리밍 프로토콜, 오디오 및 비디오 같은 미디어 리소스 식별자
- 고전적인 프로토콜로 현대에는 잘 쓰이지 않는듯

### file


### news


### telnet

- 보안 취약으로 현재는 잘 안쓰임
