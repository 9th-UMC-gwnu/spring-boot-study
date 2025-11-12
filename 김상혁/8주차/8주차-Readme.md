# API & Swagger & Annotation
## API 만들기(회원 가입 API)
1. DTO, Enum 생성
  - 요청받을 DTO와 응답할 DTO 생성
  - MemberException 생성
2. Converter 생성
  - Entity -> DTO, DTO -> Entity 생성
3. Controller 생성 1/2
  - Mapping과 ApiResponse를 이용해서 생성
4. Service 생성
  - 인터페이스를 작성하거나 구현체만 생성
5. Repository 생성
  - JpaRepository 사용
6. Service 완성 1/2
  - 서비스에서 응답 결과 (DTO)를 만드는 경우와 Controller에서 응답 결과를 만드는 경우가 존재
  - 작성하고 인터페이스에 해당 메서드를 선언
7. Controller 제작 2/2
  - DTO를 정의한 서비스와 성공 코드를 컨트롤러에 정의
8. Service 중간 완성
  - 사용자 엔티티, 선호 음식 엔티티 생성 → DB 적용
  - for 문이 stream() 사용
- 추가적으로 DB에 조회 이외 작업 (삽입, 수정, 삭제)을 할때 @Transactional 을 붙혀서 데이터 정합성을 챙김
## Swagger
- 개발한 API들의 목록을 확인하고 테스트 할 수 있는 프레임워크
- 알아서 맞는 URI와 메서드로 요청을 보내고 우리가 만든 API의 응답을 볼 수 있다.
- 오류 발생시 출력

## 어노테이션을 활용한 validation
- **@Documented** : 이 어노테이션은 사용자 정의 어노테이션을 만들 때 붙입니다.
- **@Target** : 이 어노테이션은 어노테이션의 적용 범위를 지정합니다.
- **@Retention** : 이 어노테이션은 어노테이션의 생명 주기를 지정합니다. 위의 코드는 RUNTIME이기에 실행 하는 동안에만 유효하게 됩니다.
- **@Constraint** : java에서 제공하는 사용자가 validation을 커스텀 어노테이션을 통해 할 수 있도록 제공하는 어노테이션
### Food가 있는지 그 여부를 확인하는 어노테이션
- 기존: Service에서 검증 <br>신규: DTO단에서 검증
- 검증 실패하면 MethodArgumentNotValidException을 발생

## 핵심 키워드
- java의 Exception 종류들
  - Checked Exception과 Unchecked Exception으로 나뉨
  - Checked Exception은 개발자가 반드시 예외 처리를 직접 진행해야 함
  - Exception 클래스 자체는 checked exception 이다.
  - Exception 클래스는 Throwable 클래스의 자식 클래스이고,<br> Exception 클래스의 자식 클래스 중 RuntimeException 클래스가 Unchecked Exception
  - Checked Exception
    - IOException :	파일, 네트워크 등 입출력 문제
    - SQLException : DB 접근 시 발생
    - FileNotFoundException :	지정된 파일을 찾을 수 없을 때
    - ClassNotFoundException :	클래스 로딩 실패 시
    - ParseException : 문자열 파싱 실패 시
  - Unchecked Exception
    - NullPointerException : null 객체 접근
    - ArrayIndexOutOfBoundsException : 배열 인덱스 초과
    - IllegalArgumentException : 잘못된 인자 전달
    - ArithmeticException :	0으로 나누기
    - NumberFormatException :	문자열 → 숫자 변환 실패
    - ClassCastException : 허용되지 않는데 억지로 타입 변환을 시도할 경우 발생
- @Valid
  - @Valid는 객체 내부의 필드 제약 조건(@NotNull, @Size 등) 을 검사
  - 유효하지 않으면 MethodArgumentNotValidException이 발생

# 미션
- https://github.com/hyukkimm/UMC/tree/Feat/Chapter8

# 학습 후기
- API를 만들면서 ApiResponse로 통일하면서 GeneralSuccessCode로 응답을 관리하니까 만들기 편리했다. Swagger는 많이 써보지 않아서 더 알아 봐야겠다. 
