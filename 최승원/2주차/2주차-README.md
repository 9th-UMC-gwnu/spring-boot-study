# 프레임워크란?

- 어떠한 목적을 쉽게 달성할 수 있도록 해당 목적과 관련된 코드의 뼈대를 미리 만들어둔 것
- 프레임워크에는 분명한 제어의 역전 개념이 적용되어 있어야 한다.

# API란?

- 2개 이상의 소프트웨어 컴포넌트 사이에서 상호작용 할 수 있도록 정의된 인터페이스

# 라이브러리란?

- 프로그램 개발에 필요한 함수, 클래스, 객체들을 모아둔 도구 상자와 같다.
- 프레임워크와 달리 제어권이 개발자에게 있다.

# DI, IoC, 서블릿

- 더 추상적인 것에 의존하라.

## IoC

- 프로그램의 제어 흐름을 개발자가 하는 것이 아닌 프레임워크가 관리한다.
- 개발자가 작성한 객체나 메서드의 제어를 개발자가 아니라, 외부에 위임하는 설계 원칙을 제어의 역전이라고 한다.

## DI

- Ioc 원칙을 실현하기 위한 구체적 구현 방법
- DI는 객체가 필요로 하는 것(종속성)을 직접 만들지 않고 외부에서 주입받는 방식을 말한다.

> 토비의 스프링에서 말하는 의존 관계 주입
> 1. 클래스 모델이나 코드에는 런타임 시점의 의존관계가 드러나지 않는다. 그러기 위해서는 인터페이스만 의존하고 있어야 한다.
> 2. 런타임 시점의 의존관계는 컨테이너나 팩토리 같은 제 3의 존재가 결정한다.
> 3. 의존관계는 사용할 오브젝트에 대한 래퍼런스를 외부에서 제공(주입) 해줌으로써 만들어진다.

## 서블릿

- 자바의 서블릿이란 웹 페이지를 동적으로 생성하는 서버 측 프로그램이다.

> 서블릿의 동작 방식: 스프링
> 1. 사용자가 URL을 입력하면 HTTP Request가 Servlet Container로 전송한다.
> 2. 요청을 전송 받은 Servlet Container는 HttpServletRequest, HttpServletResponse 객체를 생성한다.
> 3. web.xml을 기반으로 사용자가 요청한 URL이 어느 Servlet에 대한 요청인지 찾는다.
> 4. 해당 Servlet에서 service() 메소드를 호출한 후, 클라이언트의 GET, POST 여부에 따라 doGet() 혹은 doPost()를 호출한다.
> 5. doGet() 혹은 doPost() 메소드는 동적 페이지를 생성한 후, HttpServletResponse 객체에 응답을 보낸다.
> 6. 응답이 끝나면 HttpServletRequest, HttpServletResponse 두 객체를 소멸 시킨다.

## 서블릿 컨테이너

- 서블릿을 관리해주는 컨테이너이다.
- 서블릿 객체를 생성, 초기화, 호출, 종료하는 "생명주기"를 관리한다.
- 클라이언트의 요청(Request)를 받아주고 응답(Response)할 수 있게, 웹 서버와 Socket으로 통신한다.

## Bean

- Spring Container가 관리하는 자바의 객체
- 객체가 의존 관계를 등록할 때, Spring Container에서 해당하는 빈 객체를 찾고 그 빈과 의존 관계를 만든다.

> ApplicationContext란?
> - 스프링에서 애플리케이션을 실행할 때, 빈을 관리, 조회 등의 역할을 담당하는 인터페이스

### Bean으로 등록하여 관리하는 방식

1. @Component -> @Autowired: 묵시적 Bean 정의

#### @Component 사용해보기
```java
@Component
public class Member {
    private Integer age;
    private String name;
    private String email;
    
    public Member() {}
    
    public Member(Integer age, String name, String email) {
        this.age = age;
        this.name = name;
        this.email = email;
    }
}

// Controller Annotation은 @Component를 포함하고 있다.
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Controller {

    @AliasFor(annotation = Component.class)
    String value() default "";
}
```

2. @Configuration -> @Bean: 명시적 Bean 정의

#### @Configuration, @Bean 사용해보기
```java
@Configuration
@RequiredArgsConstructor
public class QueryDslConfig {
    
    @PersistenceContext
    private final EntityManager entityManager;

    @Bean
    public JPAQueryFactory jpaQueryFactory() {
        return new JPAQueryFactory(entityManager);
    }
}
```

## Spring IoC Container

### 동작 방식

1. 객체를 class로 정의한다.
2. 객체들 간의 연관성 지정: Spring 설정 파일(Config) 또는 어노테이션(`@Component` , `@Configuration`, `@Autowired`, `@Bean` )을 통해 객체들이 어떻게 연결될지 지정해준다.
3. IoC 컨테이너가 이 정보를 바탕으로 객체들을 생성하고 필요한 곳에 주입한다.

#### 여러 가지 의존성 주입 방법

```java
public class MemberService {
    
    // 1. 필드 주입
    private MemberRepository memberRepository;
    
    // 2. 생성자 주입
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
    
    // 3. setter 주입
    public void setMemberRepository(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

- 가장 추천하는 방식은 생성자 주입이다.
- Setter 주입은 런타임에 의존성을 주입한다. NPE를 발생시키는 원인이 될 수 있다.

## Spring Dispatcher Servlet

- Front Controller 패턴을 구현한 서블릿
- 모든 HTTP 요청을 처리하는 진입점 역할을 한다.

# 핵심 키워드

- **SOLID**
  - 객체지향 설계의 5가지 원칙
  - SRP: 단일 책임 원칙. 하나의 클래스는 하나의 책임만 가져야 한다.
  - OCP: 개방-폐쇄 원칙. 확장에는 열려있고, 변경에는 닫혀있어야 한다.
  - LSP: 리스코프 치환 원칙. 자식 클래스는 언제나 자신의 부모 클래스를 대체할 수 있어야 한다.
  - ISP: 인터페이스 분리 원칙. 특정 클라이언트에 특화된 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.
  - DIP: 의존관계 역전 원칙. 고수준 모듈은 저수준 모듈에 의존하면 안 된다. 둘 다 추상화에 의존해야 한다.

- **DI**
  - 의존성 주입. 객체가 사용할 의존 객체를 직접 생성하지 않고 외부에서 주입받는 방식.

- **IoC**
  - 제어의 역전. 객체의 생성과 관리 책임을 개발자가 아닌 프레임워크가 담당하는 것.

- **생성자 주입 vs 수정자, 필드 주입 차이**
  - 생성자 주입: 의존성을 생성자를 통해 주입. 불변성을 보장하고, 테스트가 용이.
  - 수정자 주입: 의존성을 setter 메서드를 통해 주입. 선택적 의존성에 유용.
  - 필드 주입: 의존성을 필드에 직접 주입. 코드가 간결하지만, 테스트와 확장성이 떨어짐.

- **AOP**
  - 핵심 로직과 부가 로직을 분리하는 프로그래밍 패러다임
  - 로깅, 트랜잭션 관리, 보안 등 공통 관심사를 따로 분리 해서 관리

- **서블릿**
  - 자바 기반 웹 애플리케이션에서 HTTP 요청/응답을 처리하는 표준 인터페이스.
  - 서블릿 컨테이너가 관리한다.

# 학습 후기

- 스프링 프레임워크라는 큰 숲을 멀리서 관찰한 느낌이다. 기존 공부 방식은 작은 나무 하나하나를 자세히 들여다보는 방식이었다면, 이번 주차는 전체적인 구조와 흐름을 이해하는 데 중점을 둔 것 같다. 앞으로 각 개념들을 더 깊이 파고들면서, 이 큰 그림 속에서 어떻게 작동하는지 알아가는 과정이 기대된다.

# 시니어 미션

## 전통적인 서블릿 기반 개발과 Spring MVC의 차이

### 전통적인 서블릿 기반 개발

- HttpServlet을 직접 상속받아 doGet, doPost 메서드 오버라이딩.
- 클라이언트 요청 → 서블릿 매핑(URL 기반) → 코드 안에서 요청 파라미터 처리 → 비즈니스 로직 실행 → 응답 작성(HTML, JSON, JSP forward 등).
- 개발자가 **요청/응답 객체(HttpServletRequest, HttpServletResponse)**를 직접 다룸.
- View로 이동할 때도 RequestDispatcher.forward() 같은 저수준 API를 직접 호출.

### Spring MVC

- Front Controller 패턴을 적용한 DispatcherServlet이 진입점.
- 모든 요청을 DispatcherServlet이 먼저 받고, 이후 적절한 Controller(핸들러)로 위임.
- 컨트롤러 메서드는 단순히 파라미터를 선언하면 자동으로 매핑 (@RequestParam, @ModelAttribute, @RequestBody 등).
- 결과는 Model + View 이름만 반환하면, ViewResolver가 알맞은 뷰(JSP, Thymeleaf 등)를 찾아 렌더링.
- AOP 기반으로 공통 기능(예외 처리, 로깅, 인증/인가)을 쉽게 분리 가능.

### 전통적인 서블릿 기반 개발의 어려움

1. 저수준 작업 부담
   - 모든 요청/응답 처리, 파라미터 추출, 예외 처리, 뷰 렌더링 등을 개발자가 직접 구현해야 함.
2. 관심사 혼재
   - 비즈니스 로직, 요청 처리, 응답 작성이 한 클래스/메서드 안에 섞임.
3. 재사용/확장 어려움
   - 로깅, 인증/인가, 공통 예외 처리 등을 재사용 가능한 구조로 만들기 어려움.
4. 코드 중복
   - 여러 서블릿에서 반복되는 코드를 줄이기 힘듦.

### Spring MVC가 해결하는 문제점

1. Front Controller 패턴
2. 요청 파라미터 바인딩 자동화
3. 뷰 처리 추상화
4. 공통 관심사 처리
5. 생산성 & 유지보수성 향상

### DispatcherServlet이 내부적으로 요청을 처리하는 방식 단계별 분석. (키워드: HandlerMapping, HandlerAdpater, Intercepter) 다이어그램을 그려서 단계별로 설명하기

![스크린샷 2025-09-24 17.08.05.png](/Users/seungwon-choi/Desktop/스크린샷 2025-09-24 17.08.05.png)

1. 요청 진입
2. Handler Mapping 조회
3. Handler Adapter 실행
4. Controller 내 비즈니스 로직 수행
5. 뷰 리졸버에게 뷰 반환
6. 클라이언트에게 응답 반환

## AOP(Aspect-Oriented Programming) 원리 탐구

### OOP와 AOP의 차이점 분석.

| **구분** | **OOP (객체지향)** | **AOP (관점지향)** |
| --- | --- | --- |
| 관점 | 객체 단위 | 관심사 단위 |
| 책임 분리 | 클래스/객체 단위 | 공통 관심사 단위 |
| 장점 | 구조적 캡슐화 | 중복 코드 제거, 유지보수 편리 |
| 예시 | UserService, OrderService 등 클래스 중심 | 로깅, 트랜잭션, 보안 검사 등 핵심 로직과 분리 |

### AOP의 핵심 개념(Advice, JoinPoint, Pointcut, Aspect, Weaving) 정리.

- Aspect: 공통 관심사를 모듈화한 단위(클래스 단위)
- Advice: Aspect가 실제 수행하는 행동(메서드 단위)
- JoinPoint: Advice가 적용될 수 있는 프로그램 실행 지점
- Pointcut: JoinPoint 중에서 Advice를 적용할 위치를 지정
- Weaving: Aspect와 핵심 로직을 결합하는 과정

### AOP가 적용되는 런타임 위빙 vs 컴파일 타임 위빙의 차이점 조사.

| **구분** | **컴파일 타임 위빙** | **런타임 위빙 (Spring AOP 기본)** |
| --- | --- | --- |
| **시점** | 소스코드 컴파일 시 | 애플리케이션 실행 시점(프록시) |
| **대상** | 모든 메서드/클래스 | Spring 빈 객체, 프록시 대상 |
| **구현 방식** | AspectJ 컴파일러 사용 | JDK Dynamic Proxy, CGLIB |
| **장점** | 빠른 실행, 완전 통합 | 유연, Spring 환경과 통합 용이 |
| **단점** | 유연성 떨어짐, Spring 통합 어려움 | 일부 제한 (OOP 클래스 내부 호출 시 적용 안 됨) |

### 어노테이션이 작동되는 방식을 Spring AOP를 중심으로 조사.

1. **어노테이션 정의**
    - 예: @Transactional, @Aspect, @Before, @After 등
2. **빈 생성 시점**
    - Spring IoC 컨테이너가 빈을 생성할 때, **프록시 객체(proxy)를 생성**한다.
    - 프록시는 핵심 객체를 감싸서 호출 전/후에 Advice를 삽입할 수 있도록 만든다.
3. **메서드 호출 시점**
    - 클라이언트가 컨트롤러/서비스 메서드를 호출하면 실제 호출은 **프록시**를 통해 전달.
    - 프록시 내부에서 **Pointcut 확인 → JoinPoint 매칭 → Advice 실행 → 실제 메서드 호출** 순으로 진행.
4. **실행 완료 후**
    - AfterAdvice 등 필요한 Advice 실행 → 최종 반환.

### Spring에서 AOP가 프록시 패턴(Proxy Pattern)을 활용하여 동작하는 원리 분석.

- Spring AOP는 런타임 위빙을 위해 **프록시 객체**를 사용한다.
    1. **빈 생성 시점**
        - Spring IoC 컨테이너가 Bean을 생성할 때, AOP 적용 대상이면 **핵심 객체 대신 프록시 객체**를 등록.
        - 핵심 객체는 그대로 두고, 프록시가 모든 메서드 호출을 가로챈다.
    2. **클라이언트 호출**
        - 클라이언트가 서비스 메서드를 호출하면 실제 호출은 프록시를 통해 전달.
        - 프록시는 **Advice 실행 → 핵심 메서드 호출 → 후처리 Advice 실행** 순으로 동작.
