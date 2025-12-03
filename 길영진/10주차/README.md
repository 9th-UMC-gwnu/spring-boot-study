# 🌱 로그인 및 회원 가입 (1)
---

---

---
# Spring Security
- 스프링에서 진행되는 강력한 보안 프레임워크
- 스프링 시큐리티 주요 기능
1. 누가 들어오는지(인증)
2. 들어온 사람이 어디에 갈 수 있는지(인가)
3. 위험한 상황으로부터 보호(보안 위협 방어)
# 주요 컴포넌트
  - ### AuthenticationManager
    - 인증 과정을 관리하는 중심 컴포넌트
  - ### AuthenticationProvider
    - 실제로 인증 로직을 처리하는 역할을 담당
  - ### UserDetailsService
    - 사용자 정보를 불러오고 검증하는 서비스
  - ### SecurityContext
    - 인증이 완료된 사용자 정보를 저장하는 컨텍스트
    - 여기서 **SecurityContextHolder**는 현재 보안 컨텍스트에 대한 세부 정보를 보관
    - 이를 통해 사용자 인증 정보를 개별적으로 유지
## Filter Chain
  1. **SecurityContextPersistenceFilter**
     - 요청 간 SecurityContext를 유지합니다.
     - 새 요청이 들어올 때 이전에 인증된 사용자의 정보를 복원합니다.
  2. **UsernamePasswordAuthenticationFilter**
     - 폼 기반 로그인을 처리합니다.
     - 사용자가 제출한 username과 password를 확인하여 인증을 시도합니다.
  3. **AnonymousAuthenticationFilter**
     - 이전 필터에서 인증되지 않은 요청에 대해 익명 사용자 인증을 제공합니다.
  4. **ExceptionTranslationFilter**
     - Spring Security 예외를 HTTP 응답으로 변환합니다.
     - 인증 실패 시 로그인 페이지로 리다이렉트하거나, 인가 실패 시 403 오류를 반환합니다.
  5. **FilterSecurityInterceptor**
     - 접근 제어 결정을 내리는 마지막 필터입니다.
     - 현재 인증된 사용자가 요청한 리소스에 접근할 권한이 있는지 확인합니다.
## 인증 흐름
  1. 사용자가 로그인 폼에 입력 후 제출
  2. AuthenticationFilter
  3. AuthenticationManager
  4. AuthenticationProvider
  5. UserDetailsService
  6. SecurityCContext

## 인가 흐름
1. 리소스 접근 요처
2. FilterSecurityInterceptor
3. AccessDecisionManager
4. 권한 확인
5. 접근 결정
---
# 서비스 매서드 로직 작성
- 비즈니스 로직을 작성하는 구간
- 서비스에서 응답 DTO를 만들어도 되고, 컨트롤러에서 만들어도 됨
- JPA에서 제공하는 페이징을 사용하기 위해서는 몇가지가 필요
  1. PageRequest를 생성: 페이징할 정보 (Offset, Limit을 정의)
  2. Repository에 PageRequest 삽입
---
# 핵심 키워드
## 1. Spring Security
- 스프링에서는 페이지를 나누기 위해서 Pagination을 위한 두 가지 객체를 제공한다.
- Page는 Slice를 상속한다. (Slice가 가진 메서드를 Page도 사용가능)
- 여기서 Page와 Slice가 다른점은 Page는 조회 쿼리 이후 젠체 데이터 개수를 조회하는 퀴리가 한번 더 실행된다는 것
- Slice는 조회 쿼리만 날림
- Slice와 Page 사용 점
  - Slice는 무한 스크롤 등을 구현하는 경우 유용
  - Page는 전체 페이지 개수나 데이터 개수가 필요한 경우 유용
  - 데이터 양이 많아진다면 Slice를 사용하는 것이 성능상은 유리

## 2. 인증(Authentication)과 인가(Authorization)
- 객체 모델과 데이터 모델 간의 구조적 차이 때문에 벌어지는 문제
  1. 객체 모델은 상속을 자연스럽게 지원하지만, 관계형 데이터베이스에서는 이를 직접적으로 지원하지 않는다.
  2. 객체 모델에서는 객체 간의 연관관계를 직접 참조로 표현할 수 있지만, 관계형 데이터베이스에서는 외래 키(Foreign Key)를 사용해 연관관계를 표현해야 한다.
  3. 객체는 참조 동등성을 사용하지만, 데이터베이스에서는 기본 키를 사용하여 행의 동등성을 판단한다.
  4. 객체 모델에서는 필요한 데이터를 즉시 로딩할 수 있지만, 데이터베이스에서는 조인을 통해 데이터를 가져와야 한다.
- 객체지향 프로그램은 위와 같은 이유로 객체 그래프 탐색을 할 수 없는데 JAP는 이를 해결하여 준다.
- JPA에서 단방향 연관관계 매핑은 엔티티 간의 관계를 설정하는 방식으로 한쪽이 주인이 되어 탐색
- 양방향은 단방향 연관관계 2개를 만들어 해결

## 3. 세션과 토큰
- 세션이란 사용자가 인증에 성공한 상태를 말한다.
- 토큰은 교통승차권과 같이, 무언가를 이용할 수 있는 권한이나 자격을 나타내는 징표이다.
- 세션 기반은 클라이언트가 서버로 리소스를 요청할떄, Authorization 헤더를 통해 토큰을 함께 전달하고 항상 확인한다.
- 토큰 기반은 서버가 아닌 클라이언트에서 유저의 인증정보를 관리한다.
- 토큰 기반은 서버가 유저의 인증상태를 기억하고 있을 필요 없이 서버가 가지고 있는 비밀키를 통해서 유효성만 검증하면 된다.

## 4. 엑세스 토큰(Access Token)과 리프레시 토큰(Refresh Token)
- 엑세스 토큰은 유효기간이 짧다
- 리프레시 토큰은 유효기간이 길다
- 보통 통신에는 엑세스 토큰을 사용하다가 엑세스 토큰이 만료되면 리프레시 토큰 사용
1. 로그인 인증에 성공한 클라이언트는 해당 두개의 토큰을 서버로 부터 전달받는다.
2. 클라이언트는 헤더에 엑세스 토큰을 넣어 통신을 한다.
3. 일정한 기간이 지나 엑세스 유효기간이 만료되면 리프레시 토큰을 통해 재발급 요청
4. 여기서 만약 리프레시고 만료되면 재로그인

# 학습 후기
- 학습 내용을 정리하면서 Spring Security의 인증·인가 흐름과 핵심 구성 요소를 더 확실하게 이해할 수 있었다.
- 세션·토큰 구조까지 함께 정리해 전체적인 보안 동작 방식을 한눈에 파악할 수 있어 유익했다.
# 미션
### 1주차 사진
 <img alt="erd0" src="https://github.com/mybookG/image/blob/main/erd0.png?raw=true" />

### github 링크
https://github.com/mybookG/Umc_personal_repository