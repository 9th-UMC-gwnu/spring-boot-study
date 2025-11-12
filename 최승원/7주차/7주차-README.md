# 핵심 키워드

- RestControllerAdvice
    - @ControllerAdvice + @ResponseBody 가 합쳐진 어노테이션

- lombok
    - 어노테이션 기반으로 보일러플레이트 코드를 줄여주는 라이브러리

- dto 형식 public static VS record 비교하기
    - record
        - 코드 간결, 불변 객체 자동생성, 가독성 높음
    - static class 내부 DTO
        - 네임스페이스 관리에 유리함

# 학습 후기
- API 응답 형식 통일을 통해, 팀원과의 협업이 더 원활해질 수 있다는 것을 느꼈다. 또한 에러 응답 형식도 통일이 필요하다는 것을 알게 되었다.

# 미션

- 미션 기록
  RestControllerAdvice의 장점

    1. 전역적인 예외 처리 통일
    2. 응답 형식 일관성 유지
    3. HTTP 상태 코드, 메세지 관리 집중화

  없을 경우 어떤 점이 불편한지

    1. 컨트롤러마다 동일한 예외 처리 코드 반복
    2. 응답 포맷이 컨트롤러마다 달라질 위험
    3. HTTP 상태 코드나 메시지 설계의 누락, 오류 가능성 증가

[7주차 미션 레포지토리](https://github.com/chltjsdl0119/UMC-chapter4-mission/pull/2)
