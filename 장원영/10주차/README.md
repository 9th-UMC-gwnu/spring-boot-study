# 10주차 스터디 노트

## 학습 목표
- 스프링 시큐리티로 세션 기반 로그인/회원가입 구현 흐름 이해
- 스프링 시큐리티로 JWT 기반 로그인/회원가입 구현 흐름 이해
- 세션 방식과 토큰 방식 인증의 차이점 정리

## 스터디 주제
- 세션 방식과 JWT 방식의 구조적 차이
- 왜 JWT 방식이 널리 쓰이게 되었는지

## 세션과 JWT 비교
- 상태 관리: 세션은 서버 메모리나 외부 스토리지에 상태를 들고, 클라이언트는 세션 ID만 쿠키로 보관. JWT는 서버 상태 없이 클라이언트가 서명된 토큰 자체를 보관.
- 확장성: 세션은 서버 확장이 늘면 세션 공유(Sticky Session, 세션 서버)가 필요. JWT는 서명 검증만으로 확장에 유리하지만 토큰 폐기가 어렵다.
- 보안 포인트: 세션은 세션 탈취 방어를 위해 Secure, HttpOnly, SameSite 쿠키 설정과 세션 고정 공격 방어가 중요. JWT는 서명 비밀키 관리, 짧은 만료 시간, Refresh Token 분리와 보관이 핵심.
- 업데이트/폐기: 세션은 서버에서 세션 무효화로 즉시 끊을 수 있음. JWT는 발급된 토큰을 서버가 직접 폐기하기 어려워 블랙리스트나 짧은 만료, Refresh 토큰 전략을 사용.
- 전송 비용: JWT는 클레임이 커지면 매 요청 헤더 크기가 커질 수 있음. 세션은 ID만 전송.

## 왜 JWT가 많이 쓰이는가
- 서버 무상태(stateless)로 확장성이 좋아 클라우드/마이크로서비스에 적합
- 서로 다른 서비스 간 인증 위임(게이트웨이, API 서버)에서 검증이 단순
- 모바일, SPA 환경에서 백엔드가 다수일 때 일관된 인증 토큰 형태를 제공
- 단, 즉시 폐기가 어려우므로 만료 시간 설계와 Refresh 토큰 보호가 필수

## 스프링 시큐리티 기본 원리
- Filter Chain이 요청을 가로채고 AuthenticationManager가 인증 처리, SecurityContext에 인증 결과를 담아 이후 계층에서 사용
- UserDetailsService를 통해 사용자 조회, PasswordEncoder로 비밀번호 검증
- 인가(Authorization)는 AccessDecisionManager와 설정된 SecurityFilterChain 룰로 처리

## 스프링 시큐리티 기본 활용 메모
- 세션 방식: formLogin, UsernamePasswordAuthenticationFilter 활용, 로그인 성공 시 세션에 SecurityContext 저장. CSRF 보호 기본 활성화.
- JWT 방식: UsernamePasswordAuthenticationFilter 앞에 커스텀 JWT 인증 필터 추가, 토큰 발급 엔드포인트에서 AuthenticationManager로 인증 후 Access/Refresh 토큰 반환. Stateless 세션(sessionCreationPolicy(STATELESS)) 설정과 CSRF 비활성화, CORS 설정 병행.
- 공통: 비밀번호는 BCryptPasswordEncoder 등으로 단방향 해시, 예외 처리 시 401/403을 구분해 응답.

## 미션
[10주차 미션](https://github.com/hexter31376/umc_mission4/tree/study/Chapter7)

## 소감
- 마지막 주차에서 시큐리티를 배워 인상깊었습니다... 개인적으로 스프링의 각종 프레임워크 라이브러리중 가장 방대하고 사전 지식이 많이 필요한 부분이라 어려웠지만 그만큼 보람있는 시간이었던 것 같습니다.
