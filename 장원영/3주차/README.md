Chapter 3. REST API와 엔드포인트 설계
===
목차
* API와 REST API
* URI, URL, URN 정의와 차이
* 엔드포인트 설계와 Path 변수 활용
* 스프링 프로젝트 세팅

API와 REST API
---
### API란?

API(Application Programming Interface)는 프로그램 간의 상호작용을 위해 미리 정의된 규약 또는 인터페이스를 의미합니다.
즉, 특정 기능을 어떻게 호출해야 하는가를 정의하는 일종의 약속입니다.  

우리가 다룰 분야 중 하나인 HTTP 기반에서 자주 쓰이는 API는 REST API, GraphQL, gRPC 등이 있습니다.

### REST API란?

REST(Representational State Transfer)는 리소스를 자원 단위로 표현하고 이를 HTTP 메소드와 매핑하여 통신하는 방식입니다.  
메서드 + url 조합으로 이용되며 메서드는 다음과 같이 구성됩니다.  
 
| 메서드 | 역할 | 데이터베이스 로직 |
| ---- | --- | ------------- |
| GET | 리소스 조회 | read |
| POST | 리소스 생성 | create |
| PUT/PATCH | 리소스 수정 | update |
| DELETE | 리소스 삭제| delete |

REST API의 핵심은 리소스를 엔드포인트로 식별하고, 행위는 HTTP 메서드로 표현한다는 점입니다.
그리고 이것들은 데이터베이스의 crud 개념과도 어느 정도 맞닿아 있습니다

URI, URL, URN 정의와 차이
---

### 1. URI
* 정의 : 인터넷 상의 모든 리소스를 고유하게 식별하기 위한 문자열.
* 포괄 개념: URL과 URN을 모두 포함하는 상위 개념.
* 구성 요소 :
  * 스키마(Scheme): 어떤 방식으로 접근할지 (`http`, `https`, `ftp`, `mailto`, …)
  * 권한(Authority): 도메인 이름, 포트 번호
  * 경로(Path): 리소스의 구체적 위치
  * 쿼리(Query): 요청에 필요한 부가 조건 (`?key=value`)
  * 프래그먼트(Fragment): 문서 내 특정 지점(`#section1`)
* 예시 : `https://example.com:8080/users?id=10#profile`



### 2. URL
* 정의 : 리소스의 “위치(Location)”를 나타내는 URI의 한 형태.
* 특징 :
  * 리소스가 어디에 존재하는지 알려줌.
  * 접근할 프로토콜 + 구체적인 경로 포함.
* 구성 요소:
  * `프로토콜://도메인:포트/경로?쿼리#프래그먼트`
* 예시:
  * `https://example.com/users/15`
  * `ftp://files.server.com/download.zip`
* URL은 "지도" 같은 것. "이 주소로 가면 리소스가 있다."



### 3. URN
* 정의 : 리소스의 위치가 아닌, “이름(Name)”을 기반으로 리소스를 고유하게 식별하는 URI의 한 형태.
* 특징 :
  * 리소스의 위치가 바뀌어도 **이름은 변하지 않음**.
  * 독립적이며 영속적으로 식별 가능.
* 구성:
  * `urn:<네임스페이스 식별자>:<네임스페이스 별 이름>`
* 예시:
  * `urn:isbn:0451450523` → 책 ISBN 번호로 식별
  * `urn:ietf:rfc:3986` → IETF RFC 3986 문서를 의미
* URN은 "도서관 분류번호" 같은 것. "어디 있든 ISBN 번호가 같으면 그 책이다."

엔드포인트 설계와 Path 변수 활용
---

### 엔드포인트란?

REST API에서 클라이언트가 요청을 보낼 수 있는 접점으로, URI + HTTP Method 조합으로 표현됩니다.

예:

| rest 요청 | 기능 |
| ---------- | ------------ |
| GET /users | 모든 사용자 조회 |
| GET /users/{id} | 특정 사용자 조회 |
| POST /users | 사용자 생성 |
| PUT /users/{id} | 사용자 정보 수정 |
| DELETE /users/{id} | 사용자 삭제 |

### PathVariable 활용

스프링에서 Path 변수를 활용할 때는 @PathVariable 어노테이션을 사용합니다.

``` java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    public String getUser(@PathVariable("id") Long id) {
        return "User ID: " + id;
    }
}
```

요청 예:

`GET /users/10` -> `"User ID: 10"`

즉, Path 변수는 리소스의 식별자를 경로에 포함시켜 restful하게 표현할 수 있도록 해줍니다.

스프링 프로젝트 세팅
---

스프링 부트 프로젝트를 시작할 때는 Spring Initializr를 가장 많이 사용합니다.
저는 intelij 사용자이기에 내장 세팅 툴을 이용해서 세팅하지만 이게 공식이므로 이것으로 소개드립니다. 

Spring Initializr (https://start.spring.io/)
1. Project: Gradle (Kotlin DSL or Groovy), Maven 중 선택
2. Language: Java/Kotlin
3. Spring Boot 버전: LTS(예: 3.x 이상) 선택
4. Dependencies: Spring Web, Spring Data JPA, Lombok, H2 Database 등 필요한 모듈 추가

예시: Gradle Kotlin DSL 설정

``` kotlin
plugins {
    id("org.springframework.boot") version "3.2.0"
    id("io.spring.dependency-management") version "1.1.3"
    kotlin("jvm") version "1.9.20"
    kotlin("plugin.spring") version "1.9.20"
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("org.springframework.boot:spring-boot-starter-data-jpa")
    implementation("com.fasterxml.jackson.module:jackson-module-kotlin")
    runtimeOnly("com.h2database:h2")
    testImplementation("org.springframework.boot:spring-boot-starter-test")
}
```

프로젝트를 실행하면, @RestController 기반의 REST API 엔드포인트를 바로 구현할 수 있습니다.

미션
---
> 0주차 때 제공된 IA, WF을 참고하여 API Endpoint, Request Body, Request Header, query String, Path variable 이 포함된 간단한 명세서 만들기  
> 홈 화면, 마이 페이지 리뷰 작성, 미션 목록 조회(진행중, 진행 완료), 미션 성공 누르기, 회원 가입 하기(소셜 로그인 고려 X)

### 도서 대여(커머스) 시스템 API 명세서 v1
> 2주차 ERD를 바탕으로 로그인과 유저와 메인 페이지 관련 api 명세서 작성  
> 포함 요소: Endpoint / Request Header / Request Body / Query String / Path Variable / 응답 예시

---

### 홈 화면(추천/신간 목록) – 페이징 미적용
* Method / Endpoint : `GET /api/v1/home`
* Headers :  
  `Authorization: Bearer {accessToken}` (선택)  
  `Content-Type: application/json`
* Query String : 없음

#### Response : (200)
 ```json
{
  "success": true,
  "message": "홈 화면 조회 성공",
  "data": {
    "featured": [
      { "bookId": 101, "title": "객체지향의 사실과 오해", "price": 20000, "thumbnailUrl": "/img/b101.png" },
      { "bookId": 205, "title": "HTTP 완벽 가이드", "price": 42000, "thumbnailUrl": "/img/b205.png" }
    ],
    "newArrivals": [
      { "bookId": 309, "title": "스프링 시큐리티 인 액션", "price": 36000, "thumbnailUrl": "/img/b309.png" }
    ]
  }
}
```

### 마이 페이지(내 프로필 & 포인트)
* Method / Endpoint : `GET /api/v1/users/me`
* Headers :  
  `Authorization: Bearer {accessToken}`
  `Content-Type: application/json`

#### Response : (200)
 ```json
{
  "success": true,
  "message": "마이 페이지 조회 성공",
  "data": {
    "userId": 7,
    "email": "dev@example.com",
    "nickname": "teamplay",
    "myPoint": 2500,
    "createdAt": "2025-09-01T12:00:00Z"
  }
}
```

### 회원 가입 (소셜 로그인 고려 X)
* Method / Endpoint : `POST /api/v1/users/signup`
* Headers :  
  `Content-Type: application/json`

#### Request Body
 ```json
{
  "email": "dev@example.com",
  "loginId": "teamplay01",
  "password": "1q2w3e4r!",
  "nickname": "teamplay",
  "name": "장원영",
  "gender": "M",
  "birth": "2002-01-04",
  "address": "강원도 원주시 흥업면"
}
```

#### Response : (201)
 ```json
{
  "success": true,
  "message": "회원 가입 성공",
  "data": {
    "userId": 7
  }
}
```

### 로그인 (액세스/리프레시 발급 예시)
* Method / Endpoint : `POST /api/v1/auth/login`
* Headers :  
  `Content-Type: application/json`

#### Request Body
 ```json
{
  "loginId": "teamplay01",
  "password": "1q2w3e4r!"
}
```

#### Response : (201)
 ```json
{
  "success": true,
  "message": "로그인 성공",
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6...",
    "expiresIn": 3600
  }
}
```