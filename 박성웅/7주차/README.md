# Chapter 7. API 응답 통일 & 에러 핸들러 (1)
```TEXT
1. API 응답 통일의 목적
2. 에러코드 인터페이스와 구현체
2-1. BaseErrorCode(인터페이스)
2-2. GeneralErrorCode(Enum 구현체)
3. 통일된 응답 포맷 (ApiResponse)
4. 유지보수성을 위한 DTO, Converter, 서비스 계층 분리 컨벤션
  4-1. TestResDTO 내부 public static 클래스 사용 이유
  4-2. Converter 클래스 역할
  4-3. 서비스 계층 설계와 비즈니스 로직 분리
5. 서비스 작성하기
6. 예외 구조
  6-1. GeneralException
  6-2. 도메인별 Exception
  6-3. 예시
7. 전역 예외 처리기(GeneralExceptionAdvice)
8. 코드 관리 규칙
9. 실제 동작 과정
```
## 1. API 응답 통일의 목적
- API는 서버와 프론트엔드가 데이터를 주고받는 통로
- API마다 응답 형태가 다르면 프론트엔드가 응답을 파싱하고 처리하기 불편함
- 성공과 실패가 다르면, 한 API에서도 두 가지 형식을 모두 처리해야 해 혼란스러움 
-모든 API 응답을 같은 틀로 통일하는 것이 중요 
---
## 2. 에러코드 인터페이스와 구현체
### 2-1. BaseErrorCode(인터페이스)
- 에러는 이렇게 생겨야 한다(규칙 약속)
- HTTP 상태 (getStatus), 서비스 내부 코드 (getCode), 에러 메시지 (getMessage)

### 2-2. GeneralErrorCode(Enum 구현체)
- 에러는 이렇게 생겼다(실제 목록)
- BaseErrorCode의 규칙에 따라 실제 사용될 공통 에러 목록과 값을 구체적으로 정의
- BAD_REQUEST, NOT_FOUND 등 일반적인 에러에 대해 구체적인 HTTP 상태, 코드, 메시지를 할당
---
## 3. 통일된 응답 포맷 (ApiResponse)
- `isSuccess`: 요청의 성공 여부  
- `code`: 상태 구분을 위한 고유 코드  
- `message`: 코드에 대한 사람 친화적인 설명  
- `result`: 실제 응답 데이터(JSON 형태, DTO로 구성)
```
이렇게 되면, 성공이든 실패든 같은 구조로 응답을 받게 되어 일관성이 유지
```
- 공통 응답 형태를 관리하는 클래스가 ApiResponse <br>
- result는 어떤 형태의 값이 올지 모르기에 Generic <br>
- 성공한 경우: `onSuccess()` <br>
- 실패한 경우: `onFailure()` <br>  
---
## 4. 유지보수성을 위한 DTO, Converter, 서비스 계층 분리 컨벤션
### 4-1 왜 TestResDTO 내부에 public static 클래스를 쓰는가?
- 응답/요청 DTO가 많아질수록 관리가 복잡해지기에, 관련 DTO를 하나의 바깥 DTO 클래스 내부에 static inner class로 묶으면 구조가 명확해지고 관리가 편함​
- static inner class로 만들면 바깥 DTO 인스턴스의 참조 없이 독립적으로 사용할 수 있어 메모리 성능적으로 효율적
- DTO 설계 시 "static inner class 우선 사용하라"는 베스트 프랙티스와 이유(메모리/접근성/캡슐화/관리 효율)를 반드시 함께 넣어 설명하는 게 좋음.

### 4-2. Converter 클래스의 역할
- 비즈니스 로직 또는 데이터 가공 시, 엔티티에서 바로 DTO로 변환하도록 단일 책임 클래스를 둔다.
- TestConverter에서는 파라미터(예: String)를 받아서 DTO의 빌더를 사용해 인스턴스화 시킴.
- Converter가 있으면 앞으로 엔티티에서 DTO로, 다양한 가공 작업에 대한 로직을 한곳에서 관리 가능.

### 4-3. 서비스 계층 설계와 비즈니스 로직 분리
- Query(조회)/Command(변경) 서비스 분리 패턴 
- 조회(GET)는 TempQueryService의 역할
- 나머지(CUD)는 TempCommandService로 역할 분리
- 서비스 구현 시 인터페이스에서 구현체로 가는 패턴으로, 코드의 유연성과 테스트성을 높임.
- Controller는 실제 구현체가 아닌 인터페이스를 주입받음(SDI, DI 활용).
- 유지보수, 기능 확장, 테스트코드에서 매우 유리한 설계 패턴임.
---
## 5. 서비스 작성하기
```JAVA
1. GET 요청과 나머지 요청에 대해 아래와 같이 분리한다.
	a. GET 요청에 대한 비즈니스 로직을 처리할 경우
			TempQueryService 이렇게 만든다.
	b. 나머지 요청에 대한 비즈니스 로직을 처리할 경우
			TempCommandService 이렇게 만든다.

2. 서비스를 만들 경우 인터페이스를 먼저 두고 이를 구체화 한다.
	 TempQueryService 인터페이스, TempCommandService 인터페이스를 만들고,
	 이에 대한 Impl 구체화 클래스를 만든다.

3. 컨트롤러는 인터페이스를 의존하며 실제 인터페이스에 대한 구체화 클래스는
	 Springboot의 의존성 주입을 이용한다!
```
## 6. 예외 구조
### 6-1 GeneralException  
- 프로젝트 전반에서 발생하는 공통 예외
- 예외가 발생하면 `BaseErrorCode`를 포함해 던짐
- 예외 처리기가 이를 감지해 적절한 응답 형태로 변환  

### 6-2 도메인별 Exception  
- 세부 예외는 `GeneralException`을 상속받아 만듬

### 6-3 예시
- `TestException`: 테스트 도메인 예외  
- `MemberException`: 회원 관련 예외  
- ` 어느 영역에서 , 어떤 이유로 ` 오류가 발생했는지를 명확히 추적 가능
---
## 7. 전역 예외 처리기(GeneralExceptionAdvice)
- `@RestControllerAdvice`
- 어떤 예외가 발생하더라도 프론트엔드는 항상 같은 응답 형태를 받음.

- GeneralException 처리:  
  도메인에서 발생한 모든 커스텀 예외를 잡아서,  
  `ApiResponse.onFailure()` 형태로 통일된 구조로 반환

- 그 외 Exception 처리:  
  예외로 분류되지 않은 모든 오류는 `INTERNAL_SERVER_ERROR(500)` 상태로 처리  
---

## 8. 코드 관리 규칙
```JAVA
1. common 에러는 COMMON000 으로 둔다. <- 잘 안쓰지만 마땅하지 않을 때 사용
2. 관련된 경우마다 code에 명시적으로 표현한다.
	- 예를 들어 멤버 관련이면 MEMBER001 이런 식으로
3. 2번에 이어서 4000번대를 붙인다. 서버측 잘못은 그냥 COMMON 에러의 서버 에러를 쓰면 됨.
	- MEMBER400_1 아니면 MEMBER4001 이런 식으로
```

예시:
```JAVA
// Member Error
- MEMBER_NOT_FOUND(HttpStatus.BAD_REQUEST, "MEMBER4001", "사용자가 없습니다."),
- NICKNAME_NOT_EXIST(HttpStatus.BAD_REQUEST, "MEMBER4002", "닉네임은 필수 입니다."),

// Article Error
- ARTICLE_NOT_FOUND(HttpStatus.NOT_FOUND, "ARTICLE4001", "게시글이 없습니다.");
```
---
## 9. 실제 동작 과정
1. 클라이언트가 요청을 보냄  
2. Controller가 해당 요청을 Service로 전달  
3. Service에서 조건을 검사하다가 오류 조건을 발견하면 `throw new TestException(TestErrorCode.TEST_EXCEPTION)`  
4. 예외가 발생하면 Controller로 가지 않고 전역 예외 처리기로 넘어감  
5. 예외 처리기가 `ApiResponse.onFailure()`로 변환해 응답 생성  
6. 클라이언트에는 JSON 형태로 동일한 구조의 실패 응답이 전달  
---

# 🎯 핵심 키워드
- RestContollerAdvice: 예외처리할 때, 코드 중복을 줄이고 일관된 에러 응답을 제공
- lombok: 어노테이션을 통해 getter, setter, 생성자, 빌더 패턴 등을 자동 생성
- dto 형식 public static VS record 비교하기: 복잡한 DTO가 필요하면 public static, 단순한 DTO가 필요하면 record

# 📢 학습 후기
- 클린한 아키텍처 설계 원리를 적용하려고 보니 뭔가 심리적 장벽이 있었는데 코드를 실제로 따라쳐보니까 생각보단 이해가 편했습니다.
- 실무를 하게 된다면 에러 코드와 메시지의 체계적 관리가 필요하겠구나 생각이 들었습니다.

# 🔥 미션
https://github.com/sungw00ng/UMC-week-mission/tree/FEAT/Chapter7
####  1. RestControllerAdvice의 장점, 그리고 없을 경우 어떤 점이 불편한지도 조사하여 미션 기록란에 기재하기
####  2. 지금까지 진행하면서 작성한 API들 모두 응답통일 처리하기
#### 3. 성공 메서드, 성공 Enum 제작하기

# 💪 미션 기록
- RestControllerAdvice가 없다면 각 컨트롤러 메서드마다 try {…} catch (Exception e) {…}를 달아야하는 불편함이 있음
- 반면에, RestControllerAdvice가 있다면 하나의 클래스 (GlobalExceptionHandler)로 여러 종류의 예외를 한꺼번에 처리.
