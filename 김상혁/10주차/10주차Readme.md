# Spring Security
## Spring Security란?
- Spring Security는 Spring 기반 애플리케이션의 보안을 담당하는 강력한 프레임워크
- 주로 두 가지 핵심 기능인 **인증**과 **권한 부여**를 제공
- Spring Security의 역할
  1. 누가 들어오는지 확인 (인증, Authentication)
  2. 들어온 사람이 어디에 갈 수 있는지 결정 (인가, Authorization)
  3. 위험한 상황으로부터 보호 (다양한 보안 위협 방어)
## 핵심개념
### 인증(Authentication) 
- 사용자가 제공한 크리덴셜(보통 아이디와 비밀번호)을 확인하여 신원을 검증
- `Authentication` 인터페이스는 인증된 사용자의 정보를 담고 있습니다. 
- `getPrincipal()`은 사용자의 식별 정보를, `getAuthorities()`는 사용자의 권한 정보를 반환
### 인가(Authorization)
- 인증된 사용자가 특정 리소스에 접근할 수 있는지, 특정 동작을 수행할 수 있는지를 결정
- 애플리케이션 접근 -> 인증 -> 가야할 곳으로 인가
## Spring Security 주요 컴포넌트
- AuthenticationManager
  - **인증(Authentication)** 과정을 관리하는 중심 컴포넌트
  - 사용자가 로그인을 시도할 때, `AuthenticationManager`가 사용자의 자격 증명(예: 이메일/비밀번호)을 받아 이를 인증할 수 있는 프로세스를 호출
- AuthenticationProvider
  - 실제로 인증 로직을 처리하는 역할을 담당
  - 여러 `AuthenticationProvider`가 존재할 수 있으며, 각각은 특정 인증 방식을 처리
    - 예를 들어, 기본적인 이메일/비밀번호 인증은 하나의 `AuthenticationProvider`에서 처리하고, 소셜 로그인의 경우 또 다른 `AuthenticationProvider`가 담당 가능
   
- UserDetailsService
  - 사용자 정보를 불러오고 검증하는 서비스
  - 이 서비스는 데이터베이스 또는 다른 저장소에서 사용자 정보를 가져오고, 이를 `UserDetails` 객체로 반환
    - `UserDetails` 객체: 사용자의 아이디, 비밀번호, 권한(Role) 등 다양한 정보를 포함
   
- SecurityContext
  - 인증이 완료된 사용자 정보를 저장하는 컨텍스트
  - 이 정보는 애플리케이션 전반에서 공유되며, `SecurityContextHolder`를 통해 접근
    
## SecurityContextHolder
- `SecurityContextHolder`는 현재 보안 컨텍스트에 대한 세부 정보를 보관
- 기본적으로 **ThreadLocal**을 사용하여 동일한 스레드 내에서는 각 사용자의 인증 정보를 개별적으로 유지
- 요청마다 인증된 사용자의 정보를 보존하고, 다른 요청에서는 다른 사용자의 정보를 처리할 수 있도록 함

### SecurityContextHolder의 주요 역할
1. 인증된 사용자 정보를 **SecurityContext**에 저장 및 관리
2. 이후의 요청에서 **SecurityContext**를 통해 인증된 정보를 참조하여 사용자의 권한이나 인증 상태를 확인
3. 애플리케이션의 어디서나 **`SecurityContextHolder.getContext()`** 메서드를 사용해 인증 정보에 접근 가능

## Filter Chain
### 주요 필터들
1. **SecurityContextPersistenceFilter**
    - 요청 간 SecurityContext를 유지
    - 새 요청이 들어올 때 이전에 인증된 사용자의 정보를 복원
2. **UsernamePasswordAuthenticationFilter**
    - 폼 기반 로그인을 처리
    - 사용자가 제출한 username과 password를 확인하여 인증을 시도
3. **AnonymousAuthenticationFilter**
    - 이전 필터에서 인증되지 않은 요청에 대해 익명 사용자 인증을 제공
4. **ExceptionTranslationFilter**
    - Spring Security 예외를 HTTP 응답으로 변환
    - 인증 실패 시 로그인 페이지로 리다이렉트하거나, 인가 실패 시 403 오류를 반환
5. **FilterSecurityInterceptor**
    - 접근 제어 결정을 내리는 마지막 필터
    - 현재 인증된 사용자가 요청한 리소스에 접근할 권한이 있는지 확인
### 동작 방식
1. 클라이언트로부터 요청이 들어오면, 요청은 Filter Chain의 첫 번째 필터부터 순차적으로 통과
2. 각 필터는 요청을 검사하고 필요한 작업을 수행
3. 필터는 요청을 다음 필터로 전달하거나, 특정 조건에 따라 요청 처리를 중단 가능
4. 모든 필터를 통과한 요청만이 실제 애플리케이션 로직에 도달


## Spring Security의 인증과 인가 흐름
### [인증(Authentication) 흐름]
1. **사용자 로그인 요청**
    - 사용자가 로그인 폼에 credentials(예: 이메일, 비밀번호)을 입력하고 제출
2. **AuthenticationFilter**
    - `UsernamePasswordAuthenticationFilter`가 요청을 가로채고 `Authentication` 객체를 생성
3. **AuthenticationManager**
    - `AuthenticationManager`는 적절한 `AuthenticationProvider`를 선택하여 인증을 위임
4. **AuthenticationProvider**
    - 선택된 `AuthenticationProvider`는 `UserDetailsService`를 사용하여 사용자 정보를 로드
    - 로드된 정보를 바탕으로 비밀번호를 검증
5. **UserDetailsService**
    - 데이터베이스나 다른 저장소에서 사용자 정보를 조회
6. **SecurityContext**
    - 인증이 성공하면, `Authentication` 객체가 `SecurityContext`에 저장

### [인가(Authorization) 흐름]
1. **리소스 접근 요청**
    - 인증된 사용자가 보호된 리소스에 접근을 시도
2. **FilterSecurityInterceptor**
    - `FilterSecurityInterceptor`가 요청을 가로채고 권한 검사를 시작
3. **AccessDecisionManager**
    - `AccessDecisionManager`는 현재 사용자의 권한과 요청된 리소스의 필요 권한을 비교
4. **권한 확인**
    - `SecurityContext`에서 현재 인증된 사용자의 권한 정보를 조회
5. **접근 결정**
    - 사용자의 권한이 충분하면 리소스 접근이 허용
    - 권한이 부족하면 `AccessDeniedException`이 발생하고 접근이 거부
  
## 로그인 방식
- 사용자가 ‘로그인했는지 아닌지’를 서버가 기억하는 방식에 따라 세션 기반 인증과 토큰 기반 인증이 나뉨
- 세션 기반 인증은 서버가 사용자 정보를 기억하는 Stateful이고,<br>
토큰 기반 인증은 사용자 정보를 기억하지 않는 Stateless이다.
- 최근에는 모바일 앱, 프론트엔드와 백엔드가 분리된 구조,
또는 REST API 기반 서비스가 많아지면서 토큰 기반 인증(JWT 등)을 범용적으로 더 많이 사용하는 추세
### 세션 기반 인증
- 로그인 시 서버가 세션 객체를 생성하고, 클라이언트에 쿠키를 전달
    - 이후 클라이언트의 요청마다 쿠키를 통해 사용자를 식별
- 사용자의 정보가 서버의 메모리에 저장(Stateful)
- 클라이언트는 쿠키로 인증을 계속 유지
  - 로그아웃 시에는 세션을 삭제해야 함
 
### 토큰 기반 인증
- 로그인 시 서버가 토큰을 발급하고, 클라이언트는 이를 저장
    - 이후 요청에 Authorization 헤더로 전송
- 사용자의 정보는 토큰 자체에 포함되고, 서버는 이를 저장하지 않음 (Stateless)
    - 로그아웃 시에는 토큰을 삭제하거나 서버에서 블랙리스트 관리가 필요
 

# 핵심 키워드
- Spring Security
  - Spring 기반 애플리케이션의 보안을 담당하는 강력한 프레임워크로 인증과 권한 부여 기능을 제공
  - 인증, 인가, 보안의 역할
- 인증(Authentication)과 인가(Authorization)
  - 인증은 사용자가 제공한 정보를 바탕으로 신원을 검증하는 과정이고 인가는 인증된 사용자가 특정 리소스에 관한 권한이 있는지 확인하고 보내주는 과정
- 세션과 토큰
  - 세션은 서버가 로그인 상태를 저장하는 Stateful, 토큰은 서버가 상태를 유지하지 않는 Stateless
- 액세스 토큰(Access Token)과 리프레시 토큰(Refresh Token)
  - Access Token은 빠르게 만료되는 인증 토큰
    - 유효기간이 짧음 (5분~30분)
    - API 요청 시 매번 Header에 넣어서 보냄 → Authorization: Bearer <TOKEN>
    - 탈취되면 위험하므로 짧은 시간만 유효하도록 설계
  - Refresh Token은 장기 인증용으로 Access Token이 만료되었을 때 새로운 Access Token을 재발급해주는 토큰
    - 유효기간이 길다 (일반적으로 2주~1달)
    - 서버 DB/Redis에 저장하여 관리 (보안 강화)
    - 직접 API 요청에는 사용하지 않음 → 오직 재발급용
    - Access Token의 탈취 위험을 줄여줌

# 미션
github : [미션링크](https://github.com/hyukkimm/UMC/tree/Feat/Chapter10)
# 학습후기
- Spring Security의 로그인 과정을 이해하면서 세션과 JWT 토큰 방식에 대해 알았고 다음에 프로젝트할 때 써봐야겠다
