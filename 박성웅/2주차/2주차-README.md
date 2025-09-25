# 프레임워크란?

- 어떠한 목적을 쉽게 달성할 수 있도록 해당 목적과 관련된 코드의 뼈대를 미리 만들어둔 것을 의미한다.
- Spring을 사용하면, Frame(프레임, 뼈대) 안에 Work(작업)을 하는 것이다.

> 기업에서 쓰는 규모 큰 자바 프로그램을 더 쉽게 만들 수 있도록 도와주는, <br>
> 가볍고 오픈소스로 제공되는 미리 만들어둔 코드의 뼈대를 제공하는 프로그램 개발 도구 <br>

<br><br>

```text

그럼 프레임워크와 API와 라이브러리 차이가 뭘까?
셋의 정확한 개념은 무엇일까?

```

<br>

# API란?

- 2개 이상의 소프트웨어 컴포넌트 사이에서 상호작용 할 수 있도록 정의된 인터페이스를 말한다.
- 다른 개발자들이 사용할 수 있도록 함수나 메서드, 클래스를 정의하는 것이다.

# 라이브러리란?

- 함수, 클래스, 객체들을 모아둔 도구 상자이다.
- XML 파일을 다루어야 한다면, `XML 파싱 라이브러리`를 가져다 쓰면 된다.

<br>

```text

제어권 즉, 흐름의 차이로

라이브러리의 제어권은 개발자에게 있고,
XML 파싱 라이브러리는 내가 parse() 호출해야 작동한다.

반면에 프레임워크는
개발자가 제어권을 갖는 것이 아니라, 프레임워크가 코드를 호출한다.
Spring에서 URL 요청이 들어오면 내가 정의한 함수가 자동으로 실행된다.

```

<br>

# DI, IoC, 서블릿
```text

IoC 원리로 DI가 구현되고, 서블릿은 웹에서 이를 활용하는 핵심 컴포넌트다.

```
## IoC(Inversion of Control)

- 개발자가 프레임워크가 정해놓은 규칙과 구조에 맞게 코드를 작성하고 삽입하면, <br>
실제 실행 흐름은 프레임워크가 관리한다. <br>

- Spring Boot나 Django 같은 프레임워크를 사용하면 <br>
"URL에 어떤 함수가 연결되는지"만 지정해주면 나머지는 프레임워크가 처리한다. <br><br>
- 이것을 제어의 역전, 즉 IoC라고 부른다. <br>

## DI

- 객체가 자기 안에서 필요한 것들을 직접 만들지 않고,
- 생성자 인자, 메서드, 혹은 Factory Method를 통해 외부에서 만들어진 것을 전달받아 사용한다.
- 전달받은 후에는 객체 안에 설정된 속성을 통해서만 종속성을 사용하게 된다.

```

종속? Dependency란 무엇인가?
의존 대상 B가 변하면, 그것이 A에 영향을 미친다.
 - 이일민, 「토비의 스프링 3.1」, 『에이콘(2012)』, p113

```

### Coupling(결합도)

- 강한 결합(Tight Coupling) : 본체랑 건전지가 납땜돼 있는 장난감 → 건전지 갈려면 장난감 자체를 뜯어야 함
- 느슨한 결합(Loose Coupling) : 끼워 넣는 건전지 → 그냥 빼고 새 걸 끼우면 끝

> 소프트웨어 설계에서 좋은 소프트웨어는 결합도는 낮고, 응집도가 높아야한다.

<br><br>

## 서블릿

- 자바의 서블릿이란 웹 페이지를 동적으로 생성하는 서버 측 프로그램이다. -위키피디아-

<img width="600" height="350" src="https://github.com/user-attachments/assets/f3c1d10d-eb77-4743-8d73-814929c9b260" /> <br>
그림 1. Servlet 동작 방식 <br>
이미지 출처 : https://velog.io/@1224kang/%EC%84%9C%EB%B8%94%EB%A6%BFServlet <br>

### 서블릿의 동작 방식 : 스프링
```java

클라이언트가 URL을 입력하면 →
편지를 써서 Servlet Container(편지를 관리하는 집)로 보낸다.

Servlet Container 는 요청을 받으면 →
편지를 읽고, HttpServletRequest(받은 편지)와 HttpServletResponse(답장 쓸 종이)를 준비한다.

web.xml 을 보고 →
이 편지가 어떤 Servlet(도우미)에게 가야 하는지 찾는다.
Servlet은 먼저 service()라는 문을 열고 들어가서, 편지가 GET인지 POST인지 보고 →
알맞게 doGet() 또는 doPost()로 보낸다.

doGet()/doPost()는 →
새 편지(동적 페이지)를 만들어서 HttpServletResponse에 담아 클라이언트에게 보낸다.
다 끝나면 → 사용한 편지 종이(HttpServletRequest, HttpServletResponse)는 버려서(소멸) 깨끗이 정리한다.

```

### web,xml
- 웹 애플리케이션의 배치(descriptor) 파일로, 서블릿, 필터, 리스너 등  웹 구성 요소의 설정 정보를 정의하는 환경 설정 파일이다.
- 톰캣과 같은 WAS는 이 파일을 읽어 웹 애플리케이션을 초기화하고, 요청 처리 환경을 구성한다.
- 일반적으로 `WEB-INF` 디렉터리 안에 위치한다.


## 서블릿 컨테이너

- 서블릿을 관리해주는 컨테이너이다.
- 서블릿 객체를 생성, 초기화, 호출, 종료하는 "생명주기"를 관리한다.
- 클라이언트의 요청(Request)를 받아주고 응답(Response)할 수 있게, 웹 서버와 Socket으로 통신한다.

<br><br><br><br>

# 스프링에서의 DI, IoC, 서블릿
```text

스프링은 IoC 원리로 객체 관리를 자동화하고, DI로 의존성을 주입하며, 서블릿은 웹 요청을 처리하는 핵심 역할을 한다.

```

## Bean

- Spring 컨테이너가 관리하는 자바의 객체
- `ApplicationContext.getBean()` 함수를 호출했을 때 얻을 수 있다.
- 스프링은 Bean을 통해 객체를 인스턴스화한 후, 객체 간의 의존 관계를 관리한다.


### ApplicationContext란?
- `ApplicationContext`는 스프링에서 Bean을 보관하고, 필요할 때 꺼내서 의존성을 연결해주는 컨테이너이다.

### Bean으로 등록하여 관리하는 방식

1. `@Component` → `@Autowired` : 묵시적 Bean 정의 (자동 판단) <br>
2. `@Configuration`→ `@Bean` : 명시적 Bean 정의 (직접 지정) <br><br>

<br><br><br><br>

## Spring IoC 컨테이너

### 동작 방식

1. 객체를 class로 정의한다.
2. 객체들 간의 연관성 지정: Spring 설정 파일 또는 어노테이션을 통해 객체들이 어떻게 연결될지 지정해준다.
3. IoC 컨테이너가 이 정보를 바탕으로 객체들을 생성하고 필요한 곳에 주입한다.

### POJO 기반 개발

```java

// UserService: POJO(Plain Old Java Object) 클래스

public class UserService {
    
    // 1. 필드 주입
    private UserRepository userRepository;
    
    // 2. 수정자 주입
    public setUserRepository(UserRepository userRepository) {
      this.userRepository=userRepository;
    }
    
    // 3. 생성자 주입 (객체의 불변성을 보장하므로 권장된다)
    public UserService(UserRepository userRepository) {
      this.userRepository=userRepository;
    }

    public User findUserById(Long id) {
      return userRepository.findById(id);
    }
}

```

- 우리가 직접 의존성 객체인 UserRepository를 관리하지 않아도 된다.
- 간결한 POJO로 비즈니스 로직을 구현할 수 있다.

<br><br><br>

## Spring DI

- Spring DI는 객체가 필요한 다른 객체를 스스로 만들지 않는다.
- 스프링이 대신 주입해주어 코드와 테스트를 쉽게 만드는 것이다.(IoC)
- 코드가 깔끔해지고, 객체가 서로 어떤 구현에 의존하는지 몰라도 되므로 테스트하기가 쉬워진다.
- 특히, 인터페이스나 추상 클래스를 사용하면 Stub과 같은 테스트용 가짜 객체로 쉽게 모의 구현할 수 있다.

<br><br><br>

### 생성자 주입에 대해 더 자세히
```Java
// 생성자 주입의 객체 불변성

public class MemberSignupService {
  private final MemberRepository memberRepository;

  @Autowired
  public MemberSignupService(final MemberRepository memberRepository) {
    this.memberRepository=memberRepository;

  /*
    ...
  */

}

//Lombok: @RequireArgsConstructor

import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class MemberSignupService {
    private final MemberRepository memberRepository;

    /*
    ...
    */
}

```

<br><br><br>

## Spring의 DispatcherServlet
- FrontController 패턴을 구현한 서블릿이다.
- 모든 HTTP 요청을 받는다.

```Java
@Controller
public class MyController {

  @GetMapping("/umc")
  public String hello(Model model) {
    model.addAttribute("message", "UMC Spring Fighting!");
    return "greeting";
  }
}
```

- @Controller → 이 클래스가 웹 요청을 처리하는 컨트롤러임을 스프링에 알린다.
- @GetMapping("/umc") → 사용자가 /umc URL로 GET 요청을 보내면 이 메서드가 실행된다.
- hello(Model model) → 모델에 "message"라는 이름으로 "UMC Spring Fighting!" 값을 담는다.
- return "greeting" → greeting.html 같은 **뷰(화면)**를 보여주도록 반환한다.
- 즉, 사용자가 /umc에 접속하면 "UMC Spring Fighting!" 메시지가 포함된 greeting 페이지가 보여지는 컨트롤러 코드이다.

<br><br><br><br>

# 핵심 키워드

### SOLID

  - 객체지향 설계의 5가지 원칙
  - SRP: 하나의 클래스는 하나의 책임만 가져야 한다. 즉, 클래스는 하나의 변경 이유만 가져야 한다. 
  - OCP: 소프트웨어 엔티티(클래스, 모듈, 함수 등)는 확장에는 열려 있어야 하고, 수정에는 닫혀 있어야 한다. <br>
         즉, 기존 코드를 변경하지 않고 기능을 확장할 수 있어야 한다. <br>
  - LSP: 자식 클래스는 부모 클래스를 대체할 수 있어야 한다. <br>
         즉, 자식 클래스는 부모 클래스의 기능을 확장하거나 수정하여도 부모 클래스의 기능을 대체할 수 있어야 한다. <br>
  - ISP: 클라이언트는 자신이 사용하지 않는 인터페이스에 의존하지 않아야 한다. <br> 
         즉, 하나의 일반적인 인터페이스보다 여러 개의 구체적인 인터페이스를 사용하는 것이 좋다. <br>
  - DIP: 고수준 모듈은 저수준 모듈에 의존해서는 안 된다. 둘 다 추상화에 의존해야 하며, 추상화는 세부 사항에 의존하지 않아야 한다. <br>
         즉, 의존 관계를 역전시켜야 한다. <br>

### DI
  - 객체가 자기 안에서 필요한 것들을 직접 만들지 않고 외부에서 가져온다.

### IoC
  - "URL에 어떤 함수가 연결되는지"만 지정해주면 나머지는 프레임워크가 처리한다.

### 생성자 주입 vs 수정자, 필드 주입 차이
  - 생성자 주입: 객체가 생성될 때 필요한 의존성을 모조리 설정해버린다. 객체의 불변성을 보장한다.
  - 수정자 주입: 객체의 필드를 직접 생성자에서 주입하지 않고, setter 메서드를 통해 의존성을 주입.
  - 필드 주입: 런타임에 의존성을 주입하여 의존성이 없더라도 객체가 생성될 수 있지만, 순환 참조 문제가 발생할 수 있다.

### AOP
  - 핵심 기능과 공통 기능을 분리해 코드 중복을 줄이고 유지보수를 쉽게 해주는 기법

- **서블릿**
  - 웹 페이지를 동적으로 생성하는 서버 측 프로그램

<br><br><br>

# 학습 후기
```text

1. DI 방식을 깊게 보았습니다.
DI 방식에 따라 코드 작성과 테스트를 훨씬 수월하게 만든다는 점이 가장 인상적이었습니다.
생성자 주입, 수정자 주입, 필드 주입의 차이를 직접 비교하면서 각각의 장단점을 이해할 수 있었고,
DispatcherServlet과 서블릿 컨테이너가 웹 요청을 처리하는 흐름에 대해 실전 코드를 따라해보며
실제 웹 애플리케이션의 구조가 더 명확하게 이해되었습니다.

2. AOP가 뭔지.. 잘 모르겠습니다.
로깅만 맛보기로 사용한 것 같습니다.. 실전에서 더 다뤄보고 싶네요.

```

<br><br><br>

# 실습 체크리스트
- [x] 생성자 주입 연습해보기

<br><br>

# 실습 인증
- [x] 생성자 주입 연습해보기

<img width="800" height="600" src="https://github.com/user-attachments/assets/be3a99ad-c27d-4222-bad4-7da56fefd8cd" /> <br>

<br><br>

# 트러블 슈팅
`이슈`
- NoSuchBeanDefinitionException: No qualifying bean of type 'week2.repository.MemberRepository' 

`문제`
- MemberRepository가 인터페이스라서 스프링은 바로 Bean으로 만들 수 없다. <br>
  즉, MemberService에서 생성자 주입하려고 하면 NoSuchBeanDefinitionException이 발생한다. <br>

`해결`
- @Repository 붙여서 Spring이 Bean으로 관리 

`참고레퍼런스`
- https://hellowworlds.tistory.com/150


