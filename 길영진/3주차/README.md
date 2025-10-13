# 🌱API URL의 설계 & 프로젝트 세팅

# API란?

- 응용 프로그램에서 사용 할 수 있도록, 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 뜻한다.
---
# REST API

- REST API는 HTTP 요청을 통해 통신하여 리소스 내에서 레코드를 생성하고 읽기, 업데이트 및 삭제와 같ㅌ은 표준 데이터베이스 기능을 수행합니다.
- HTTP URI를 통해 자원을 명시하고, HTTP Method를 통해 해당 자원에 대한 CRUD Operation을 적용한 것을 의미
---
# HTTP 메소드

### GET
>요청이 들어오면 서버에서 데이터를 전달 이때 쿼리 스트링을 무방비로 노출되므로 주의

### POST
> Post 요청은 데이터를 처리하고, 메시지 바디를 통해 데이터를 전달하고 주로 신규 리소스를 생성하고, 등록하고, 업데이트하는데 사용한다.
>쿼리 파라미터는 key-value 형식으로 되어있다.

### PUT
>리소스가 있으면 대체하고, 없으면 생성합니다. 대체할 때, 전체 내용을 갱신합니다.

### PATCH
> PUT요청과 비슷하게 리소스를 수정하는 역할을 수행하지만 리스로를 부분 변경한다는 점에서 차이가 있습니다.
> PATCH를 지원하지 않는 곳은 POST요청을 사용한다.

### DELETE
> 리소스를 제거할 때 사용

### 멱등성!!
> 요청을 한 번을 호출하든 여러 번을 호출하든 그 결과가 같음을 의미한다. 즉, 동일한 요청을 한번 보내는 것과 여러번 연속으로 보내는 것이 같은 효과를 가지고, 서버의 상태도 동일하게 남을 때 해당 HTTP 메서드가 멱등성을 가진다고 말한다.

---

# RESTful API Endpoint의 설계
### API Endpoint
- API가 두 시스템이 상호작용하게 해주는 인터페이스라면, ENDPOINT는 API가 서버에서 리소스에 접근할 수 있게 해주는 URL이라고 할 수 있다.
- URI: 하나의 자원을 고유하게 식별하는 문자열을 의미 / URL: 자원의 위치를 식별하는 문자열 / URN: 자원의 고유한 이름을 식별하는 문자열
- REST API의  Endpoint 규칙
>1. URI에 **동사가 포함이 되어선 안된다.**
>2. URI에서 **단어의 구분이 필요한 경우 -(하이픈)을 이용**한다.
>3. **자원**은 기본적으로 **복수형으로 표현**한다.
>4. 단 하나의 자원을 **명시적으로 표현**을 하기 위해서는 **/users/id와 같이 식별 값을 추가로 사용**한다.
>5. **자원 간 연관 관계가 있을 경우 이를 URI에 표현한다.**

### API URL Endpoint
- ***로그인***
>POST /auth/users/login
>또는
>POST /auth/login
- ***회원 탈퇴***
> DELETE/autj/users

> 여기서 HardDelete와 SoftDelete가 있는데 하드는 삭제를 바로 진행 하여 용량 부담을 줄이지만 복구 어려움
> 소프트는 데이터를 삭제했어요 라는 표시만 남기는 것 해당 데이터를 조회할 떄 표시만 보고 삭제 여부를 알 수 있고 복구하기 쉬움
- N:M인 경우는 비즈니스 로직상 더 중요한 대상을 계층 관계에서 앞에 두는 방법
- Path Variable
> 단 하나, 특정 대상을 지목할 때 사용  
> GET /users/articles/{article-id}
- Query String
> 게시글 중 이름에 umc가 포함된 게시글들을 조회하려고 할 때
> GET /users/articles?name=umc&owner=ddol
- Request Body
>  request body에 JSON형식으로 담아서 서버로 전송합니다
>> {
"name" : "김주헌",
"phoneNum" : "010-1111-2222",
"nickName" : "mark",
}
- Request Header
> request header는 서버와 전송 시 메타데이터,
> 즉, **전송에 관련된 기타 정보들이 담기는 부분**입니다.
- ### API 설계 예시
  - API Endpoint | PATCH /users/{userId}
  - Request Header | Authorization : Bearer {accessToken}
---

---

# 학습 후기
- 스프링 기초에 대해서 더 배울 수 있게 되어서 좋았고, 이번을 통해 원래 알고 있다고 생각했던 내touch 용에 대해서 한번 더 짚고 넘어갈 수 있게 되었다.
- Request Header에 대한 개념이 굉장히 부족했는데 헤더에 담기는 정보가 무엇이고 어떤 형태인지 알 수 있게 되어서 API에 대한 이해도를 높일 수 있었다.
---
# 미션

### 홈화면
- API Endpoint | GET /user/{userId}/home
- Request Body | 없음
- Request Header | Authorization: Bearer {accessToken} Contestnt-Type application/json
- query String | /user/{uesrId}/home?sort=recent
- Path variable | {userId}
```java
{"message": "홈 조회 성공",
"data": {        
        "location": "안암동",
        "myPoint": 999,
        "missionCount": 7,
        "myMissions": 
        [ { "missionId": 1, "storeName": "반이학생마라탕", "storeType": "중식당","name": "10000원 이상의 식사시", "status": "IN_PROGRESS", "point": 500, "endDate": "2025-10-02"},
        { "missionId": 2, "storeName": "반이학생마라탕", "storeType": "중식당","name": "10000원 이상의 식사시", "status": "IN_PROGRESS", "point": 500, "endDate": "2025-10-02"},
        { "missionId": 3, "storeName": "반이학생마라탕", "storeType": "중식당","name": "10000원 이상의 식사시", "status": "IN_PROGRESS", "point": 500, "endDate": "2025-10-02"} ] }}
```
### 마이페이지 리뷰 작성
- API Endpoint | POST /user/{userId}/reviews
- Request Body 
```java
{
    "score":5,
    "content": "음식이 매우 맛있고 좋아요",
    "image": [imageUrl]
}
```
- Request Header | Authorization: Bearer {accessToken} Contestnt-Type application/json
- query String | 없음
- Path variable | {missionId} 
### 미션 목록 조회(진행중)
- API Endpoint | GET /users/{userId}/missions
- Request Body | 없음
- Request Header | Authorization: Bearer {accessToken} Contestnt-Type application/json
- query String | /users/{userId}/missions?status=IN_PROGRESS
- Path variable | {userId}
```java
{"message": "진행 미션 조회 성공",
"data": 
        [ { "missionId": 1, "storeName": "반이학생마라탕", "storeType": "중식당","name": "10000원 이상의 식사시", "status": "IN_PROGRESS", "point": 500, "endDate": "2025-10-02"},
        { "missionId": 2, "storeName": "반이학생마라탕", "storeType": "중식당","name": "10000원 이상의 식사시", "status": "IN_PROGRESS", "point": 500, "endDate": "2025-10-02"},
        { "missionId": 3, "storeName": "반이학생마라탕", "storeType": "중식당","name": "10000원 이상의 식사시", "status": "IN_PROGRESS", "point": 500, "endDate": "2025-10-02"} ] }
```
### 미션 목록 조회(진행 완료)
- API Endpoint | /users/{userId}/missions
- Request Body | 없음
- Request Header | Authorization: Bearer {accessToken} Contestnt-Type application/json
- query String | /users/{userId}/missions?status=COMPLETED
- Path variable | {userId}
```java
{"message": "진행 끝 미션 조회 성공",
"data": 
        [ { "missionId": 4, "storeName": "반이학생마라탕", "storeType": "중식당","name": "10000원 이상의 식사시", "status": "CONPLETED", "point": 500, "endDate": "2025-10-01"},
        { "missionId": 5, "storeName": "반이학생마라탕", "storeType": "중식당","name": "10000원 이상의 식사시", "status": "CONPLETED", "point": 500, "endDate": "2025-10-01"},
        { "missionId": 6, "storeName": "반이학생마라탕", "storeType": "중식당","name": "10000원 이상의 식사시", "status": "CONPLETED", "point": 500, "endDate": "2025-10-1"} ] }
```
### 미션 성공 누르기
- API Endpoint | POST /missions/{missionId}
- Request Body | 없음
- Request Header | Authorization: Bearer {accessToken} Contestnt-Type application/json
- query String | 없음
- Path variable | {missionId}
```java
{"message":"미션 완료",
 "data":{
    "storeId": 9999999
        }       
}
```
### 회원 가입 하기
- API Endpoint | POST /auth/users
- Request Body
```java
{
  "username": "user01",
  "password": "password123",
  "email": "user01@example.com",
  "nickname": "홍길동"
}
```
- Request Header | Contestnt-Type: application/json
- query String | 없음
- Path variable | 없음
```java
{
  "userId": 1,
  "username": "user01",
  "nickname": "홍길동",
  "createdAt": "2025-10-02"
}
```

