Chapter 7. JPA 시작
===
  
RestControllerAdvice
* 컨트롤러 전역에 적용되는 예외 어드바이스. @ExceptionHandler 메서드로 JSON 실패 응답 표준화.
* 장점: 중복 제거, 일관성, 관측성(로그/메트릭 결합).
* 미적용 시: 컨트롤러마다 try-catch 필요, 응답 포맷 불일치·누락 위험 증가.

Lombok
* 보일러플레이트 제거(@Getter, @Builder, @AllArgsConstructor 등).
* 장점: 가독성·개발속도↑, 실수↓. 고려: IDE 플러그인, 롬복 의존성 관리, 불변성 전략 명시 필요.

DTO: public static class vs record
* public static class + @Builder
* 장점: 선택적 필드/디폴트/가독성, 직렬화 친화적, Lombok Eco와 궁합.
* 단점: 롬복 의존, 보일러 약간 남음.
* record
* 장점: 불변/간결/패턴 매칭 우호.
* 단점: 선택적 필드/빌더 패턴 부자연, 일부 직렬화/프레임워크 레거시 이슈.
* 결론: Response는 class+Builder, Request는 record를 주로 권장.

### 미션
[7주차 미션](https://github.com/hexter31376/umc_mission4/tree/study/Chapter7)

### 후기
기본적인 mvc 구조를 제대로 익혀봄과 동시에 익셉션 핸들링도 진행해보아 의미있는 시간이었던 것 같습니다.