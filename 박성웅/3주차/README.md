# Chapter 3. API URL의 설계 & 프로젝트 세팅
## REST API
- 웹 서비스를 설계하는 데 사용되는 아키텍처 스타일
- 자원과 행위를 명확히 분리하여 통신하는 것이 핵심

## HTTP 메서드
| 메서드 | 역할                                      | 멱등성 (Idempotency)                   | 주요 사용처                          |
|--------|-------------------------------------------|----------------------------------------|--------------------------------------|
| GET    | 자원을 조회합니다. (Read)                 | O (여러 번 호출해도 결과 변화 없음)   | 홈 화면 조회, 목록 조회, 단일 상세 조회 |
| POST   | 새로운 리소스를 생성하거나, 데이터를 처리합니다. | X (매번 새로운 리소스 생성)           | 회원 가입, 리뷰 작성, 미션 성공 처리     |
| PUT    | 자원 전체를 대체하거나 생성합니다.         | O (요청마다 최종 상태는 동일)         | 전체 정보 수정                        |
| PATCH  | 자원의 일부만 수정합니다.                 | X (부분 수정이므로 누적될 수 있음)     | 닉네임 변경, SOFT DELETE (상태 변경)  |
| DELETE | 자원을 제거합니다.                        | O (제거 요청마다 최종 상태는 동일)     | 실제 데이터 삭제 (HARD DELETE)       |

<br><br><br>

## API Endpoint(자원 식별)
- 서버 내 자원을 가리키는 URL

### 1. API Endpoint 설계 규칙 
- 자원은 명사를 사용하고, 복수형으로 표현한다.
>/user/get [X] || /users [O]
- 단어가 복합될 경우 -(하이픈)을 사용한다.
> /userProfile [X] || /user-profile [O]
- API가 변경될 가능성에 대비하여 버전을 명시한다.
> api/v1/users

### 2. API Endpoint 식별과 연관 관계 표현
- 단일 자원 식별: 특정 자원 1개 지정 시 식별 값(ID)를 사용
> GET /users/{userId} || @PathVariable
- 자원 간 연관: User가 다른 Post를 소유할 때 경로에 표현
> GET /users/{userId}/posts || @PathVariable

### 3. API Endpoint 데이터 전달 방식(요청 상세 조건)
```text
클라이언트가 서버에 데이터를 전달하는 3가지 방식.
```
- PathVariable: URL 경로 일부로, 단 하나의 특정 자원을 식별
> GET,POST,DELETE || @PathVariable
- Query String: 여러 자원을 필터링하거나 정렬, 페이징 정보 전달
> 주로 GET || @RequestParam
- Request Body: URL에 노출되지 않게 데이터(JSON) 본문을 전달
> 주로 POST,PUT,PATCH || @RequestBody

### 4. API Endpoint Request Header
```text
클라이언트와 서버 간의 통신에 필요한 메타 정보를 담음
```
- Authorization: 사용자의 인증토큰(JWT)을 담아 인증/인가 처리
> 모든 인증이 필요한 API
- Content-Type: Request Body가 어떤 형식인지(application/json)을 명시
> POST,PUT,PATCH 요청

<br><br><br>

## 학습후기
```text
직관적인 구조가 코드로 구현되는 과정을 체감했습니다.
명확하게 매핑하기 어려웠던 부분은 미션 목록의 @RequestParam과 회원가입의 @RequestBody처럼
어노테이션들의 역할과 사용 시점을 구분하는 것이었습니다.
시간이 오래 걸릴 것 같아 Gemini의 도움을 받고 코드 뼈대만 빠르게 맞추어보았습니다.
```

<br><br><br>

## ✅ 실습 체크리스트
- [x] api/v1/users 엔드포인트 설계 규칙 준수


## ☑️ 실습 인증
<img width="1440" height="900" alt="스크린샷 2025-10-01 오후 10 59 18" src="https://github.com/user-attachments/assets/89a929cc-798b-4769-ae76-d65e0b685cb3" /><br>

<br><br><br>

## 🔥 미션

```text
홈 화면, 마이 페이지 리뷰 작성, 미션 목록 조회(진행중, 진행 완료), 미션 성공 누르기,
회원 가입 하기(소셜 로그인 고려 X)
```

<br>

```text
API Endpoint, Request Body, Request Header, query String, Path variable
이 포함된 간단한 명세서 만들기
```

## 💪 미션 기록
```text
-- API 명세서 --
1. 회원 가입
Method: POST
Endpoint: /api/v1/users/signup
Body: email, password, nickname, name
Header: 없음
Query: 없음
Path Variable: 없음

2. 홈 화면 조회
Method: GET
Endpoint: /api/v1/home
Body: 없음
Header: Authorization
Query: 없음
Path Variable: 없음

3. 리뷰 작성
Method: POST
Endpoint: /api/v1/reviews
Body: targetType, targetId, rating, content
Header: Authorization
Query: 없음
Path Variable: 없음

4. 미션 목록 조회
Method: GET
Endpoint: /api/v1/missions
Body: 없음
Header: Authorization
Query: status (in_progress, completed)
Path Variable: 없음

5. 미션 성공 처리

Method: POST
Endpoint: /api/v1/missions/{missionId}/complete
Body: proofImageUrl, memo, completedDate
Header: Authorization
Query: 없음
Path Variable: missionId (Long)
```

### json 전송 테스트 (200 OK)
- param = k:status, v: in_progress
- header = k:Authorization,v: Bearer TEST_TOKEN 

<br>

<img width="1440" height="900" src="https://github.com/user-attachments/assets/8b8d09ba-0e12-4d3a-8b48-3daf5e460206" /> <br>










