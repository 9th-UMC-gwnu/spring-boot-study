# 핵심 키워드

- java의 Exception 종류들
    1. Checked Exception

        | IOException | 파일이나 네트워크 I/O 중 오류 발생 |
        | --- | --- |
        | FileNotFoundException | 존재하지 않는 파일 접근 |
        | SQLException | 데이터베이스 액세스 중 오류 |
        | ClassNotFoundException | 클래스 로더가 클래스를 찾을 수 없음 |
        | InterruptedException | 스레드가 대기 중 인터럽트됨 |
        | ParseException | 문자열을 파싱 중 실패 |
        | ReflectiveOperationException | 리플렉션 관련 작업 중 오류 |

    2. Unchecked Exception

        | NullPointerException | null 객체 참조 |
        | --- | --- |
        | IllegalArgumentException | 메서드 인자 값이 유효하지 않음 |
        | IllegalStateException | 객체의 현재 상태가 메서드 호출에 부적절 |
        | ArrayIndexOutOfBoundsException | 배열 인덱스 범위 초과 |
        | ClassCastException | 잘못된 형변환 |
        | NumberFormatException | 숫자 변환 실패 (예: "abc" → int) |
        | ArithmeticException | 0으로 나누는 연산 등 수학적 오류 |
    3. Error
         
        | OutOfMemoryError | JVM 힙 메모리 부족 |
        | --- | --- |
        | StackOverflowError | 재귀 호출 과다 |
        | VirtualMachineError | JVM 내부 오류 |
        | NoClassDefFoundError | 클래스 로딩 실패 |

- @Valid

  jakarta.validation.Valid로, 객체 단위로 검증을 트리거하는 어노테이션.

  동작 원리

    1. @Valid가 붙은 파라미터가 컨트롤러에 들어오면, Spring MVC는 내부적으로 **Bean Validation Provider**(예: Hibernate Validator)를 사용해 검증 수행.
    2. DTO의 각 필드에 선언된 제약 조건(@NotNull, @Size 등)을 확인.
    3. 하나라도 위반되면 ConstraintViolationException 또는 MethodArgumentNotValidException을 던짐.
    4. @ExceptionHandler나 @ControllerAdvice로 이를 처리해 사용자 친화적인 응답을 생성.