# 크롤러와 크롤링
## 정의
- 재귀적으로 동작하는 자동화 웹페이지 조회 프로그램 총칭
- 인터넷 검색 엔진이 가장 대표적으로 크롤러를 통해 정보를 수집하는 주체

## 동작 방식과 다양한 특징
- 루트 집합 수집 : 검색엔진입장에서 제공자들에게 루트집합을 얻음으로서 손쉽게 많은 양의 크롤링을 할 수 있음
- 링크 추출과 상대 링크 정상화
- 순환, 루프 중복을 피하기 위해 다양한 기법들을 사용
- 효율적인 크롤링과 정보 수집을 위해 다양한 자료구조도 사용한다. 특히 병렬 수행시 적절한 동작을 위한 파티셔닝도 필수
- URL 정규화 등으로 중복 수집 방지

  # 로봇의 HTTP
  - User-Agent : 서버에게 요청을 만드는 로봇의 이름 제공
  - From: 로봇의 관리자 정보
  - Accept: 로봇이 관심있는 유형의 컨텐츠 명시
  - Referer: 현재의 요청 Url을 포함한 문서의 URL 제공

  # 로봇 차단하기

## robots.txt  
> Robots Exclustion Standard, robots.txt

- robots.txt 예시
      
      User-agent: * # 로봇의 이름

      # 명시되지 않으면 허용된것으로 간주
      Disallow: /article/write
      Disallow: /article/edit
      Disallow: /article/delete
      Disallow: /edit/category
      Disallow: /login
      Disallow: /comment/write
      Disallow: /comment/delete
      Disallow: /article/%3Ehttps://agilemanifesto.org/iso/ko/manifesto.html
      Allow: /
      User-agent: Mediapartners-Google
      Allow: /
      
      User-agent: bingbot
      Crawl-delay: 30

- 어떤 로봇이 서버의 어떤 부분에 접근할 수 있는지에 대한 정보
- 일반적으로 v1.0 표준을 따르는 편

## Html 로봇 제어 메타태그
- NOINDEX : 해당 페이지 처리 금지
- NOFOLLOW : 해당 페이지가 링크한 페이지 크롤링 금지
- INDEX: 인덱싱 허용
- FOLLOW : 링크한 페이지 크롤링 허용
- NOARCHIVE : 로컬 사본 금지
- ALL : 허용
- NONE : 불가

# 검색 엔진의 기본적인 동작방식

## 병렬 크롤링
## 기본적인 검색엔진 아키텍처

![image](https://github.com/Tobystudy/Http-Study/assets/85499582/344a2018-804b-4412-aa35-5db154b564ae)

## 인덱싱
### 역인덱싱
- 풀텍스트에서 인덱싱할 요소들을 파싱해 인덱싱하는 방식
- 엘라스틱 서치도 기본적으로 위와같음
- 루닌 참조
- 인덱스 스코어링이 존재

### 페이지 랭크 알고리즘
- 인덱싱 과정에서 관련도가 높은 순서 스코어를 매기고 정렬 방식을 제공

> 위의 여러 아이디어를 잘 조합하면 나만의 검색엔진을 만들수도 있다!
