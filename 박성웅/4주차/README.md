# Chapter 4. JPA 기초 및 프로젝트 구조
## 학습 목표
- JPA의 개념, 필요성에 대해 이해한다.
- Spring data JPA를 이용하여 Entity 설계, 매핑을 한다.

## 객체지향 언어와 RDB 패러다임은 일치하지 않는다.
- 자바는 캡슐화, 상속, 다형성으로 객체 모델링이 목표
- RDBMS는 정규화된 스키마로 데이터 구조화가 목표
> 반복 작업 : CRUD 쿼리와 매핑을 수동으로 작성 (JDBC 예시: 비즈니스 로직보다 SQL 코드가 더 크다) <br>
> 매핑 불일치: DB는 FK로 간단, 객체는 복잡한 연관관계 필요 <br>
> RDB의 상속 부재, 자바와 RDB의 타입 불일치(LocalDateTime <> DATE) <br>

## JPA(Java Persistence API)
- 자바에서 ORM(Object-Relational Mapping)을 표준화하기 위해 정의된 인터페이스
- 자체 구현체는 없으며, 대표적인 구현체로는 오픈소스로 된 Hibernate
- SQL을 직접 작성하지 않고도 객체와 RDB 테이블을 자동으로 매핑
- Entity 객체를 기반으로 데이터베이스 CRUD를 처리하고, 개발자는 비즈니스 로직에 집중 가능

## Spring 프로젝트 설계 및 구조
#### 파사드 패턴 구조
- 여러 서비스를 하나의 진입점(Facade)으로 감싸 단순하게 호출
```JAVA
// Facade
public class MemberFacade {
    private final MemberService service;

    public MemberFacade(MemberService service) {
        this.service = service;
    }

    public void register(String name) {
        service.validate(name);
        service.save(name);
    }
}
```
#### 도메인형 구조
- 도메인 객체 중심 설계, 비즈니스 로직을 Entity에 담음
```JAVA
// Domain
public class Member {
    private String name;
    public Member(String name) { this.name = name; }
    public void changeName(String newName) { this.name = newName; }
}
```
#### 계층형 구조
- Controller → Service → Repository 계층으로 분리
```JAVA
// Controller
@RestController
public class MemberController {
    private final MemberService service;
    public MemberController(MemberService service) { this.service = service; }

    @PostMapping("/member")
    public void register(@RequestParam String name) {
        service.register(name);
    }
}
```
#### 헥사고날 구조
- 비즈니스 로직(Core)과 외부 인터페이스(Infra)를 분리 <br>
```JAVA
// Port (interface)
public interface MemberRepositoryPort {
    void save(String name);
}

// Adapter (infrastructure)
public class JpaMemberRepository implements MemberRepositoryPort {
    public void save(String name) { /*저장로직*/ }
}

// Application Core
public class MemberService {
    private final MemberRepositoryPort repo;
    public MemberService(MemberRepositoryPort repo) { this.repo = repo; }
    public void register(String name) { repo.save(name); }
}
```

## Springboot 프로젝트 컨벤션
- 여러 사람들이 알고 있어야 하는 지식, 규칙을 정의하는 과정
- 컨벤션 종류: 코드 컨벤션, 깃 컨벤션, 패키지 컨벤션

> ` @Entity ` : 이 클래스가 JPA의 Entity임을 의미 <br>
>` @Table ` : DB의 테이블을 정의 <br>
>` @Id ` : DB의 PK 부분을 의미 <br>
>` @GeneratedValue ` : PK 값을 JPA가 자동으로 생성하도록 설정 <br>
>` @Column ` : DB의 속성 부분을 의미 <br>
>` @Enumerated ` : Enum을 사용할 때, 데이터의 형태를 명시화 <br>
>` @NoArgsConstructor ` : 기본 생성자 자동 생성 <br>

## JPA 연관관계
> ` @OneToOne ` : 1:1 
> ` @OneToMany ` : 1:N
> ` @ManyToOne ` : N:1
> ` @ManyToMany ` : N:M

## 연관관계의 주인(Owner)
- 양방향 연관관계를 지녔을 때 정함
- 주인은 @JoinColumn을 가진 @Entity
- 반대쪽은 mappedBy로 매핑만 하는 @Entity 
```JAVA
// 주인
@Entity
public class Order {
    @Id @GeneratedValue
    private Long id;

    @ManyToOne
    @JoinColumn(name = "member_id")  //FK를 관리하는 주인
    private Member member;
}

// 반대쪽
@Entity
public class Member {
    @Id @GeneratedValue
    private Long id;

    @OneToMany(mappedBy = "member") //읽기 전용 관계인 반대쪽
    private List<Order> orders = new ArrayList<>();
}
```

## Cascade(영속성 전이)
```JAVA
@OneToMany(mappedBy = "member", cascade = CascadeType.ALL)
```
- 부모 Entity에게 수행한 작업을 자식 Entity에게도 자동 수행 적용

## 🎯 핵심 키워드
### 계층형 구조 vs 도메인형 구조
- 계층형 구조 : 각 계층이 명확한 책임과 역할을 가지고 서로 분리
- 도메인형 구조 : 비즈니스 로직과 규칙을 Entity 자체에 집중시키는 설계 방식

### JPA
- SQL을 직접 작성하지 않고도 객체와 RDB 테이블을 자동으로 매핑

### N+1 문제
- 지연 로딩 설정으로 연관 데이터가 개별 쿼리 실행됨
- 보통 Fetch Join 등으로 한번에 쿼리 줄임

### 기본 키 생성 전략
```JAVA
- @GeneratedValue(strategy=GenerationType.IDENTITY) // 1->2->3->...
```

## 📢 학습 후기
- JPA를 통해 자바 객체와 RDB의 패러다임 불일치를 해결하는 방법을 배울 수 있어서 좋았습니다.

## 🔥 미션
- https://github.com/sungw00ng/UMC-4th-Week-ORM-Mission
