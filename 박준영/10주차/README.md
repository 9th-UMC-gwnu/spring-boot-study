# Security

## SecurityConfig

- `@EnableWebSecurity`
    - SecurityConfig 클래스에 붙는 어노테이션
    - 스프링 애플리케이션에 웹 보안 기능을 활성화할 때 사용


- SecurityFilterChain
    - `authorizeHttpRequests()`
        - HTTP 요청에 대한 접근 제어를 설정
        - `requestMatchers()` : 특정 URL 패턴에 대한 접근 권한을 설정
            - `permitAll()` : 인증 없이 접근 가능한 경로를 지정합니다.
            - `hasRole("ADMIN")` : ADMIN 역할을 가진 사용자만 접근 가능하도록 제한합니다.
            - `anyRequest().authenticated()` : 그 외 모든 요청에 대해 인증을 요구합니다.

    - `formLogin`
        - 폼 기반 로그인에 대한 설정



---

# 로그인 과정

## 1. Http Request

사용자가 `/login` 또는 인증이 필요한 URL에
**아이디/비밀번호를 들고 요청을 보냄.**

---

## 2. AuthenticationFilter에서 UsernamePasswordAuthenticationToken 생성

`AuthenticationFilter`(Spring Security 기본: `UsernamePasswordAuthenticationFilter`)가 요청을 가로채서

* email(username)
* password 를 뽑아낸 후,

**`UsernamePasswordAuthenticationToken` 객체로 포장**

이 토큰은 아직 인증 안된 상태 → `authenticated = false`.

---

## 3. AuthenticationManager

필터는 이 토큰을 **AuthenticationManager**에 전달.

AuthenticationManager는 인터페이스이고
실제로 일하는 구현체는 **ProviderManager**.

---

## 4. ProviderManager가 적절한 AuthenticationProvider 찾기

프로젝트 안에는 여러 AuthenticationProvider가 존재할 수 있어:

* DaoAuthenticationProvider (DB 기반 인증)
* JWT AuthenticationProvider
* OAuth2 AuthenticationProvider
* LDAP AuthenticationProvider
  …

ProviderManager는
**내가 가진 Provider들 중에서 이 토큰을 인증시킬 수 있는 Provider를 선택함.**

---

## 5. AuthenticationProvider → UserDetailsService 호출 

DB 기반 인증일 경우 `DaoAuthenticationProvider`가 선택되고,

이 Provider는
**UserDetailsService.loadUserByUsername()**
를 호출해서
DB에서 User를 찾아오라고 시킴.

---

## 6. UserDetailsService가 UserDetails 반환

UserDetailsService는 DB를 조회해서

```java
UserDetails user = new CustomUserDetails(memberEntity);
```

이런 형태로 **UserDetails 객체를 생성해서 반환**함.

UserDetails는 "Spring Security에서 사용하는 User 모델"이라고 이해하면 됨.

---

## 7. AuthenticationProvider가 비밀번호 검사

Provider는 UserDetails에서 꺼낸 패스워드와
요청에서 들어온 패스워드를 비교함.

`PasswordEncoder.matches(raw, encoded)`

→ 비밀번호 일치하면 인증 성공

→ 불일치하면 BadCredentialsException 발생

---

## 8. 인증 성공 → Authentication 객체 생성

성공하면 Provider는 새로운 Authentication 객체를 만들어서 반환함.

`UsernamePasswordAuthenticationToken`의
`authenticated = true` 가 된 버전이라고 보면 됨.

이 안에는:

* username
* 역할(role)
* 권한(authorities)
* UserDetails 객체

전부 들어 있음.

---

## 9. AuthenticationManager → AuthenticationFilter로 반환 (번호 9)

ProviderManager는 성공 결과 Authentication 객체를
다시 AuthenticationFilter에게 돌려줌.

---

## 10. SecurityContextHolder에 Authentication 저장 (번호 10)

마지막으로 필터는:


SecurityContextHolder.getContext().setAuthentication(authentication);


이렇게 **인증된 Authentication을 SecurityContext에 저장**함.

이제부터 요청이 끝날 때까지,
그리고 세션이 유지되는 한,

**“현재 로그인한 사용자 정보”**는
어디서든 SecurityContextHolder에서 꺼낼 수 있음.

---

# 최종 요약

```
[1] 사용자 로그인 요청
   ↓
[2] 필터가 username/password를 꺼내 토큰 생성
   ↓
[3] AuthenticationManager에 인증 요청
   ↓
[4] Manager가 적절한 Provider 선택
   ↓
[5] Provider가 UserDetailsService 호출
   ↓
[6] UserDetailsService가 DB에서 유저 조회
   ↓
[7] Provider가 비밀번호 검증
   ↓
[8] 인증 성공 → 인증된 Authentication 생성
   ↓
[9] Manager → Filter로 반환
   ↓
[10] SecurityContextHolder에 저장 → 로그인 완료
```

---

## JWT/ 세션의 차이점

| 구분             | JWT                            | 세션(Session)                         |
| -------------- | ------------------------------ | ----------------------------------- |
| **상태관리 방식**    | Stateless (서버가 유지할 상태 없음)      | Stateful (서버가 상태를 기억함)              |
| **저장 위치**      | 클라이언트(앱/브라우저)                  | 서버 메모리/Redis/DB                     |
| **서버 확장성**     | 매우 좋음 (서버 여러 대여도 문제 없음)        | 안 좋음 (세션 공유 필요 → 세션 클러스터링/Redis 필요) |
| **보안성**        | 탈취 시 위험(만료까지 유효)               | 서버가 마음대로 무효화 가능                     |
| **로그아웃 처리**    | 어려움(토큰 자체를 무효화 못함) → 서버별 관리 필요 | 쉬움 (세션 삭제하면 끝)                      |
| **속도**         | 빠름 (DB조회 없음)                   | 느림(세션 조회 필요)                        |
| **사용자 수 많을 때** | 적합                             | 부적합(세션 저장 공간 부담 큼)                  |
| **모바일 앱**      | 매우 적합                          | 부적합                                 |

---

# 핵심 키워드

- **Spring Security**

  웹 어플리케이션의 인증과 인가를 관리하는 보안 프레임워크

- **인증(Authentication)과 인가(Authorization)**

  인증 : 사용자의 신원을 확인하는 절차

  인가 : 인증된 사용자가 특정 자원에 접근할 수 있는지 확인하는 절차

- **세션과 토큰**

  세션 : 서버가 사용자의 인증 정보를 메모리에 저장하여 인증하는 방식

  토큰 : 로그인시 서버가 토큰을 클라이언트에 전달하고 해당 토큰이 유효한지, 만료되었는지 확인하는 방식

- **액세스 토큰(Access Token)과 리프레시 토큰(Refresh Token)**

  액세스 토큰 : 사용자가 인증되었음을 증명하는 짧은 만료시간의 토큰

    - 만료시간이 짧음
    - 매 요청마다 서버가 검사
    - 토큰 자체에 사용자의 정보가 들어가 있어 유출 시 위험 → 만료시간이 짧은 이유

  리프레시 토큰 : 사용자가 인증되었음을 증명하는 짧은 만료시간의 토큰

    - 만료 시간이 김
    - 서버 저장소에 저장 → 유출시 만료시키기 위해


---

# 학습 후기

- 스프링 시큐리티에 대해 많이 부족함을 느끼는 기회가 되었다.

--- 

# 미션 링크

[https://github.com/Capstone-Study-Mate/BE]