# API
## API란? 
- Application Programming Interface의 약자로 Application 을 Programming을 할 때 사용되는 인터페이스
- 예시로 Js의 console.log, python의 print 등의 함수 존재
- 추상화, 캡슐화의 개념도 포함

## REST API

- Representational State Transfer의 약자로 HTTP를 기반으로 하는 웹 서비스 아키텍처를 의미하며<br>
HTTP 메소드와 자원을 이용해 서로 간의 통신을 주고받는 방법
## 멱등성
- 멱등성(Idempotency)는 동일한 연산을 여러번 수행해도 결과가 달라지지 않는 성질
- 데이터의 일관성과 안전성을 보장하는데 핵심적인 역할
- 네트워크 지연이나 오류로 인해 같은 요청이 중복해서 전송될 경우에 시스템의 안정성을 고려하기 위해 멱등성을 고려
- HTTP 메소드 중 GET · PUT · DELETE 메소드는 멱등성을 가지며, PATCH · POST 메소드는 멱등성을 가지지 않는다.
- 자동 복구 메커니즘, 서버 문제시, 클라이언트가 같은 요청을 다시 해도 되는가의 판단 근거
  
## HTTP 메소드
- REST 방식으로 통신할 때 필요한 작업을 표시하는 방법으로 <br>
GET, POST, PUT, PATCH, DELETE 등이 있다. <br>이 메소드들은 CRUD(생성, 조회, 갱신, 삭제)에 대응
### POST
- CRUD의 Create와 대응하고 새로운 리소스를 생성할 때 사용하고, 상황에 따라 Update로도 쓰인다.
- Post 요청은 데이터를 처리하고, 메시지 바디를 통해 데이터를 전달
- 주로 신규 리소스를 생성하고, 등록하고, 업데이트하는데 사용
- 쿼리 파라미터는 key-value 형식

### GET
- CRUD의 READ와 대응하고 리소스를 조회할 때 사용
- Get 요청에서 서버에 데이터를 전달하는 경우, 쿼리 스트링을 통해서 전달하고, 이 쿼리 스트링은 무방비로 노출되므로, 조심해야 한다.<br>
바디를 통해 데이터를 전달할 수 있지만, 지원하지 않는 곳이 많아 비권장

### PUT
- CRUD의 Update와 대응하고 전체 리소스를 교체할 때 사용하지만, 리소스가 없으면 Create로 동작
- 리소스가 있으면 대체하고, 없으면, 생성합니다. 대체할 때, 전체 내용을 갱신합니다.<br>
만약 A,B의 데이터가 존재했는데 C라는 데이터를 담아 put을 보낸다면, A,B가 삭제되고,
C로 대체됩니다. 멱등성을 가집니다.

### PATCH
- CRUD의 Update와 대응하고 PUT과 다르게 리소스를 부분 변경
- PATCH 메서드를 지원하지 않는 서버에서는 POST를 사용
- PUT과 다르게 컬럼에 +값을 여러번 요청하게 되면 계속 더해지므로 멱등성을 지니지 않는다.

### DELETE
- CRUD의 Delete와 대응하고 리소스를 제거할 때 사용

---
## RESTful API Endpoint의 설계
### API Endpoint
- API 가 두 시스템(어플리케이션)이 상호작용하게 해주는 인터페이스라면,<br>
ENDPOINT는 API가 서버에서 리소스에 접근할 수 있게 해주는 URL이라고 할 수 있다.
### EndPoint
- Endpoint는 커뮤니케이션 채널의 한 쪽 끝으로 서비스를 이용할 때 사용하는 커뮤니케이션 채널의 한쪽 끝에 해당하는 URI
### URI, URL, URN
#### URI
- 하나의 자원을 고유하는 식별 문자열로 URL과 URN을 포함하는 상위 개념

#### URL
- 자원의 위치(Location)을 식별하는 문자열
- 주로 웹에서 사용되며, 특정 자원의 위치와 접근 방법을 지정
- URL 구조는 아래와 같다 <img width="832" height="247" alt="image" src="https://github.com/user-attachments/assets/93691dab-ecb2-47d6-8967-bc8af4376938" />
  - **Scheme** : 자원에 접근하기 위한 통신 프로토콜을 의미 (예: `http`, `https`, `ftp`, `sftp`)
  - **Host** : 서버의 도메인 이름 또는 IP 주소를 의미
  - **Port** : 자원에 접근하기 위한 포트 번호를 의미 (기본값은 생략 가능)
  - **Path** : 서버 내 자원이 위치하는 경로를 의미
  - **Query** : 자원에 대한 추가 질의를 위한 `key=value` 형식의 데이터를 의미
  - **Fragment** : 자원의 특정 부분을 가리키는 값으로, 서버에는 미전송

#### URN
- 자원의 고유한 이름을 식별하는 문자열
- 자원의 위치가 변경되어도 식별가능하게 설계되어 있다.
- 활용이 까다로워 사용 사례는 제한적
- URN의 구조는 다음과 같이 구성됩니다.
    - **Scheme** : 항상 `"urn"`으로 고정
    - **NID (Namespace Identifier)** : 자원이 속한 네임스페이스를 의미 (예: `isbn`)
    - **NSS (Namespace Specific String)** : 네임스페이스 내에서 자원을 고유하게 식별하는 문자열을 의미 (예: ISBN 번호 자체)
#### 포함관계
- URI는 모든 URL, URN을 포함하고 URN과 URL은 서로 부분적으로 포함관계이다.
- 현대 웹 표준에서는 URL이라는 단어로 통일해서 사용 권장

## RESTful한 API 설계 규칙
1. URI에 **동사가 포함이 되어선 안된다.**
2. URI에서 **단어의 구분이 필요한 경우 -(하이픈)을 이용**한다.
3. **자원**은 기본적으로 **복수형으로 표현**한다.
4. 단 하나의 자원을 **명시적으로 표현**을 하기 위해서는 **/users/id와 같이 식별 값을 추가로 사용**한다.
5. **자원 간 연관 관계가 있을 경우 이를 URI에 표현한다**

### 예시(회원가입, 로그인, 탈퇴)
- 회원가입, 로그인, 탈퇴는 사용자 인증과 관련된 로직
- 인증과 관련된 로직의 엔드포인트는 /auth/... 으로 나머지 API 기능들의 엔드포인트는 /api/...로 빼는 편

#### 회원가입
- 주체가 되는 것은 사용자이므로 url상에 users가 포함
- 러프하게 생각하면 POST /auth/users or POST /auth/signup
- POST /auth/users/id가 아닌 이유?<br> 해당하는 유저의 식별id는 회원가입 후에 생성되므로 API 호출 시점에서는 설계 불가 
#### 로그인
- 클라이언트가 로그인에 필요한 정보를 서버로 넘겨 로그인에 대한 처리를 요청하는 것이니 **POST로 설계**
- POST /auth/users로 설계하면 회원가입과 중복되므로 타이트하게 RESTful한 API 설계를 따르지 않아도 됨<br>
POST /auth/users/login 또는 /POST /auth/login 으로 만들어도 무관
#### 회원탈퇴
- 회원탈퇴는 DLELETE /auth/users로 설계
- HardDelete와 SoftDelete 두가지 방법이 존재

### 리소스 간 연관 관계가 있는 경우
1. 교사가 여러 개의 과목을 강의하는 1:N 관계의 경우
  - /users/subjects 로 설계 가능
  - 특정 교사의 교과목 목록은 /users/id/subjects로 설계 가능
  - 교과목 하나의 단건 조회 API를 설계한다면 특정 교사의 교과목 또는 특정 교과목 중 의미가 더 잘 전달되는 것 중 요구 사항에 더 맞게 설계<br>
  /users/id/subject/id 또는 /users/subject/id 로 설계 가능
2. N:M 인 경우
  - 비즈니스 로직 상 더 중요한 대상을 계층 관계에서 앞에 배치
  - 게시글과 해시태그의 경우 해시태그는 부수적이므로 /articles/hash-tag로 설계 가능

### 세부적인 API 설계
1. **path variable**
    -
    - 게시글 하나 상세 조회 API의 경우 GET에 단건 조회라는 것을 캐치하고 어떻게 특정 게시글임을 서버에게 전달하는지 결정할 때 사용
    - 하나의 특정 대상을 지목할 때 /users/articles/id 처럼 게시글의 식별 값을 넣어서 전달 ex)GET https://umc.com/users/articles/4<br>여기서 4가 DB 상에서 게시글의 기본키
    - 실제 API 명세서에는 GET /users/articles/{articleId} 또는 {article-id}를 대신 넣을 수 있다
2. **query string**
    -
    - 쿼리 스트링은 게시글 중 이름에 umc가 포함된 게시글들을 조회하려고 할 때, 즉 하나만 조회하는 것이 아닐 때 사용
    - 보통 검색 조회 때 사용 되며 GET /users/articles?name=umc&owner=ddol 이런식으로 &로 여러 개를 연결 할 수도 있다
    - 쿼리 스트링은 API 엔드 포인트에 포함되지 않기 때문에 엔드 포인트 자체는 GET /users/articles 로 설계
3. **request body**
    -
    - POST 방식의 경우는 url에 해당 정보들을 노출되지만, Request Body는 url에 노출 되지않고 해당 데이터를 json이나 form-data 형태로 보낸다
    - {
	"name" : "김주헌",
	"phoneNum" : "010-1111-2222",
	"nickName" : "mark",
}같이 데이터를 서버로 전송
4. **request header**
    -
    - Request Header는 서버와 전송시 메타데이터, 즉 전송에 관련된 기타 정보들이 담기는 부분
    - Request Body의 형식(Content-Type), 요청을 보낸 곳(Origin), 인증에 필요한 데이터를 넣는 경우도 존재
    
- API endpoint에 포함 되는 것은 path variable 

### API 설계 예시(닉네임 변경 API)
1. API EndPoint
    - PATCH /users/{userId}
2. Request Body
   - {<br>"nickname" : "mark"<br>}
3. Request Header
   - Authoriztion : bearer {accessToken}
   - accessToken은 로그인 된 사용자가 로그인 된 상태라고 알려주는 것으로 Authriztion이라는 키에 대한 값으로 헤더에 담아서 서버에 전송
4. Query String
   - 필요 없음

---
## 미션
https://app.swaggerhub.com/apis/su-e40/UMC-3week/1.0.0
- 회원가입
  - API Endpoint : POST /auth/users/signup
  - Request Body : {
  "username": "user123",
  "password": "mypassword",
  "email": "user@example.com",
  "nickname": "상혁",
  "preferences": ["강릉"]
} 
  - Request header : x
  - query String : x
  - Path variable : x
- 홈 화면
  - API Endpoint : GET /users/{userId}/home
  - Request Body : x
  - Request header : Authorization: bearer {accessToken}
  - query String : x
  - Path variable <br> {userId} : 띄울 홈화면의 사용자 Id
- 미션 목록 조회
  - API Endpoint : GET /users/{userId}/missions
  - Request Body : x
  - Request header : Authorization: bearer {accessToken}
  - query String : status : all, ongoing, completed
  - Path variable <br> {userId} : 조회할 사용자 Id
- 미션 성공 누르기
  - API Endpoint : POST /users/{userId}/missions/{missionId}/complete
  - Request Body : { "userId" : 1,"missionId : 101 }
  - Request header : Authorization: bearer {accessToken}
  - query String : x
  - Path variable <br> {userId} : 사용자 ID<br>{issionId} : 성공 처리할 미션 ID
- 미션 리뷰 작성
  - API Endpoint : POST /users/{userId}/missions/{missionId}/review
  - Request Body : {"rating": 5, "comment": "재미있다"}
  - Request header : Authorization: bearer {accessToken}
  - query String : x
  - Path variable  <br>{userId} : 사용자 Id <br>{missionId} : 리뷰할 미션 ID
# 학습 후기
- API를 들어보긴 했는데 잘 몰랐었는데 개념을 알게 되었고 RESTful한 API 설계 규칙에 관한 예시를 통해 어떤식으로 명세서를 작성해야 하는지 알게 되어서 유익했다.
