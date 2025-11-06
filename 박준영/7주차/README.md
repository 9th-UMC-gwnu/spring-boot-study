# API 응답 통일 & 에러 핸들러

## API 형태 

- **code**: HTTP 상태 코드 외에 더 세부적인 결과를 알려주기 위해 사용
- **message**: code에 추가적으로 어떤 결과인지 알려주기 위해 사용
- **result:** 응답에 필요한 json (DTO와 같은 것)

    {
      "isSuccess" : Boolean 타입
      "code" : String
      "message" : String
        "result" : {응답으로 필요한 또 다른 json}
    }


## 프로젝트 구조

### BaseErrorCode

- 인터페이스로, 최소한의 구현 메서드를 정하는 역할을 부여

### GeneralErrorCode 
- enum 클래스로, BaseErrorCode를 구현하여 구체적인 에러 코드와 메시지를 정의.
- @Getter, @AllArgsConstructor 사용해 불변 객체로 관리.

### ApiResponse
- 모든 API 응답을 동일한 구조로 감싸는 클래스
- 제너릭타입을 사용해 다양한 타입의 응답 데이터(result)를 처리 가능

### TestConverter
- 객체 → DTO 변환
- 빌더 패턴으로 DTO 인스턴스를 생성

### Service 작성 규칙

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


## 예외처리

- 예외 발생 → 터뜨리는 역할
- 각 도메인별 Exception 정의, 프로젝트 Exception 상속
  - 어떤 도메인의 예외인지 식별하기 위함

## 에러 핸들러

- 모든 도메인 Exception을 모아서 응답 통일하고 브라우저에게 전달하는 객체


# 핵심 키워드

- RestContollerAdvice

  - **프로젝트 전체의 예외를 감지하고, 일관된 JSON 응답으로 변환해주는 글로벌 예외 처리기**

- lombok

  - Java에서 반복되는 코드를 자동으로 생성해주는 **코드 단축 라이브러리**

    - **주요 어노테이션**
        | 어노테이션 | 설명 |
        | --- | --- |
        | @Getter | 모든 필드의 getter 메서드 자동 생성 |
        | @Setter | 모든 필드의 setter 메서드 자동 생성 |
        | @ToString | toString() 자동 생성 |
        | @EqualsAndHashCode | equals(), hashCode() 자동 생성 |
        | @NoArgsConstructor | 기본 생성자 자동 생성 |
        | @AllArgsConstructor | 모든 필드를 인자로 받는 생성자 자동 생성 |
        | @RequiredArgsConstructor | `final`또는`@NonNull`필드만 인자로 받는 생성자 생성 |
        | @Builder | 빌더 패턴 생성 |
        | @Data | `@Getter + @Setter + @ToString + @EqualsAndHashCode + @RequiredArgsConstructor` 통합형 |

- dto 형식 public static VS record 비교하기
    | 구분 | `public static class` DTO | `record` DTO |
    | --- | --- | --- |
    | **가변성** | 가변 객체 가능 (`@Setter`) | 완전 불변 |
    | **코드 간결성** | Lombok 필요 | Lombok 불필요, 더 간결 |
    | **상속/다형성** | 가능 | 불가능 (`final`) |
    | **빌더 패턴** | 지원 (`@Builder`) | 미지원 (직접 구현 필요) |
    | **JPA 호환성** | 우수 (Entity ↔ DTO 변환 용이) | 제한적 (프록시, Lazy Loading 비호환) |
    | **직렬화/역직렬화** | 완전 호환 | 일부 Jackson 버전에서 주의 필요 |
    | **목적** | CRUD 중심의 유연한 데이터 전달 | 읽기 전용, API 응답 전용 데이터 전달 |
    | **가독성/유지보수성** | 명시적 구조, DTO 그룹화 용이 | 단순 데이터 매핑에 최적화 |


# 미션기록
- 미션링크 : https://github.com/nemm3623/UMC-chapter4-mission/tree/Feat/Chapter7


# 학습 후기

- 에러 핸들러와 도메인 익셉션 등 새로운 개념에 대해 이해하고 배울 수 있는 기회가 되어 좋았다.
