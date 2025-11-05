# 🌱API 응답 통일 & 에러 핸들러 (1)

# API 응답 통일
- 상태코드는 따라 인터페이스로 정의 후 enum으로 관리
- Response는 따로 코드로 빼서 만들어준다.

---

## 임시 API 만들어보기
1. 응답 DTO 생성
   - ReqDTO랑 ResDTO 생성
2. Converter 생성
   - 응답 DTO를 만들 Converter를 만든다.
   - 파라미터에 입력을 받아 DTO 빌더 패턴으로 조립하는 과정
3. Controller 완성
   - Contoller는 해당 DTO를 응답하여 보내준다.

---
## 예외처리
- 각 도메인별 Exception을 정의, 프로젝트 Exception 상속
- 메인 프로젝트 예외를 만들고 각 프로젝트들은 이 예외를 상속받아서 사용한다.
- 이를 통해 어떤 도메인에서 발생한 예외인지 알 수 있고 Exception을 나누고 BaseErrorCode로 상세 설명
- 여기서 Exception응답을 통일할 예외 핸들러가 필요하다.
- 
## 예외 핸들러
- 기본적 원리
  1. 정의한 Exception을 감지, 탐색
  2. 미리 정의한 로직 실행
- 각 예외는 메인 예외를 상속받기 때문에 메인 예외를 처리하는 메소드를 만들어 로직 실행

--- 
## @RestControllerAdvice
- 모든 Exception을 JSON형식으로 응답, 이를 위해 @RestControllerAdvice를 이용
- 이를 위해 규격화된 RepEntoty를 이용한다.

# 핵심 키워드
### 1. RestControllerAdvice
- 먼저 @ControllerAdvice부터 이해하자
  - 모든 컨트롤러 클래스가 공유하는 공통 로직을 정의할 때 사용한다.
  - 주로 예외처리와 바인딩 설정, 모델 객체 등에 사용된다.
  - 예외를 AOP를 적용해 예외를 전역적으로 처리할 수 있는 어노테이션이다.
  - 여기서 @ControllerAdvice와 @ResponseBody가 붙어 있으면 RestControllerAdvice이다.
- 응답이 들어오면 Json으로 내려준다.
- Spring은 스프링 예외를 미리 처리해둔 ResponseEntityExceptionHandler를 추상 클래스로 제공하고 있다. 
- ResponseEntityExceptionHandler에는 스프링 예외에 대한 ExceptionHandler가 모두 구현되어 있으므로 ControllerAdvice 클래스가 이를 상속받게 하면 된다.
- 에러 메세지는 반환하지 않으므로 스프링 예외에 대한 에러 응답을 보내려면 아래 메소드를 오버라이딩 해야 한다.
- 장점을 일관되게 작성해보자면
  1. 예외 처리를 한곳에 모음
    - @RestController에서 발생하는 예외를 중앙에서 한번에 처리할 수 있다.
    - 예외 처리 로직을 한 클래스에 모아둘 수 있다.
  2. 코드 중복이 감소
    - try-catch를 매번 작성하지 않아 코드가 짧아짐
    - 예외 처리 코드를 공동화하여 중복을 줄여줌
  3. 응답 일관성 유지
    - 모든 예외에 대하여 응답형태를 JSON구조로 내보내어 일관성을 유지시켜 준다.
  4. 유지 보수 용이
    - 예외 처리 로직이 한 곳에 모여있으므로, 새로운 예외 추가 및 변경하려면 한 파일만 수정하면 됨
```java
public abstract class ResponseEntityExceptionHandler {
    ...
    protected ResponseEntity<Object> handleExceptionInternal(
        Exception ex, @Nullable Object body, HttpHeaders headers, HttpStatus status, WebRequest request){
        ...
    }
}
```
### 2. lombok
- Lombok이란 어노테이션 기반으로 코드 자동완성 기능을 제공하는 라이브러리
- 개발을 하다 보면 반복적으로 사용하는 기본적인 코드 블록이 생기는데 이러한 코드 블럭은 Lombok을 통해서 줄일 수 있다.
- Lombok의 장점
  - 어노테이션 기반의 코드 자동 생성을 통한 생산성 향상
  - 반복되는 코드 다이어트를 통한 가독성 및 유지보수성 향상
  - Getter, Setter 외에 빌더 패턴이나 로그 생성 들 다양한 방면으로 활용 가능
- Lombok 기능
  - **@Getter**, **@Setter**
    - Setter : 객체의 변수 값을 변경할 때 사용
    - Getter : 객체의 변수 값을 읽어올 떄 사용
  - **@AllArgsConstructor**
    - 모든 변수를 사용하는 생성자를 자동완성 시켜주는 어노테이션
  - **@NoArgsConstructor**
    - 어떠한 변수도 사용하지 않는 기본 생성자를 자동완성 시켜주는 어노테이션
  - **@RequiredArgsConstructor**
    - 특정 변수만을 활용하는 생성자를 자동완성 시켜주는 어노테이션
    - 생성자의 인자로 추가할 변수에 @NonNull 어노테이션을 붙여서 해당 변수를 생성자의 인자로 추가
  - **@ToString**
    - 어노테이션을 활용하면 클래스의 변수들을 기반으로 ToString 메소드를 자동으로 완성시켜 준다
  - **@Builder**
    - 어노테이션을 활용하면 해당 클래스의 객체의 생성에 Builder패턴을 적용시켜준다.

### 3. dto 형식 public static VS record 비교하기
- DTO
  - DTO는 애플리케이션의 다양한 부분 간에 데이터를 이동시키는 데 사용되는 객체
  - 애플리케이션의 계층 간에 데이터를 운반하는 컨테이너
- Record
  - 불변 데이터를 간결하고 읽기 쉽게 담는데 초점을 맞춤
  - 생성자, Getter, equals(), hashCoede(), toString() 메소드를 자동 생성
- 차이
  1. 불변성
    - Record는 기본적으로 불변성을 가진다. 한 번 만들어진 Recored 인스턴스의 데이터는 변경불가
    - 이것으로 Record는 데이터 일관성을 유지
    - DTO는 일반적으로 가변성을 가진다. 객체가 생성된 후에도 필드를 변경할 수 있다.
    - DTO를 의도적으로 불변으로 만드려면 Setter 메서드를 사용하지 않거나 final 필드 사용
  2. 보일러플레이트 코드
     - Record는 위에 설명하였듯이 Getter, Setter, 생성자, equals(), hashCode(), toString() 메서드를 자동생성해준다.
     - Dto는 직접 코드를 작성해야 한다. 이를 안하기 위해서는 Lombok 같은 도구를 사용
  3. 데이터 표현 방식
    - Record는 데이터를 간결하고 직관적으로 표현하는 방법을 제공.
    - Record 선언에는 필드만 포함디므로 코드가 더 깔끔하고 읽기 쉬움
  4. 커스터마이징
    - DTO는 커스터마이징이 용이하다 데이터 유효성 검사, 데이터 변환 메서드 또는 비즈니스 로직을 추가할 수 있다.
    - Record는 가볍고 불변성을 유지하도록 설계되었기 때문에 내부 상태를 수정하거나 복잡한 로직을 추가하기 힘듬
    - Record의 커스터마이징은 보통 외부에서 처리된다.
---

# 학습 후기
- QueryDSL을 활용하면 타입 안전성과 자동완성을 통해 동적 쿼리를 훨씬 직관적으로 작성할 수 있다는 것을 알게 되었다.
- 특히 transform과 groupBy를 이용한 DTO 매핑과 커스텀 페이지네이션 방식이 실무에서 유용하게 쓰일 것 같다.
# 미션
### 1주차 사진
 <img alt="erd0" src="https://github.com/mybookG/image/blob/main/erd0.png?raw=true" />

### github 링크
https://github.com/mybookG/Umc_personal_repository