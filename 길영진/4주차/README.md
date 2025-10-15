# 🌱JPA 기초 및 프로젝트 구조

# 객체 지향 언어와 관계형 데이터베이스
## 객체 지향언어와 RDB 패러다임의 불일치
- 이것이 일어나는 이유는 자바와 데이티베이스의 특징 때문에 일어난다,
- 테이블 형식으로 저장된 데이터를 조회하여 자바 객체로 변환하여 사용하는데 이때 생기는 문제점들이 있다
  - 개발자는 하나하나마다 SQL 퀴리를 짜야하고, 맵핑시켜주어야한다.
  - 데이터 베이스에서는 하나의 외래키로 두개를 조인할 수 있지만 자바는 연관된 클래스를 필드로 선언해야만 다른 객체로 이동할 수 있다.
  - 위 말은 즉 객체에서는 연관관계의 주인이 필요하다
  - 객체 지향 언어에서 사용하는 것과 자바에서 사용하는 패러다임의 차이가 존재한다.

---
## JPA

- 자바 진영에서 ORM 기술의 표준으로 사용되는 인터페이스의 모음

#### ORM(Object Relational Mapping) 기술이란?
- 우리의 애플리케이션의 클래스와 RDB(Relational DataBase)의 테이블을 매핑시켜준다.
- 애플리케이션의 객체를 RDB 테이블에 자동으로 영속화 해주는 것
- 현재는 Hibaernate를 많이 사용하는 추세이다.
---
# Spring 프로젝트 설계 및 구조
- 프로젝트 구조
  - 파사드 패턴 구조
  - 도메인형 구조
  - 계층형 구조
  - 핵사고날 구조
---
## springboot 프로젝트 컨벤션
- 코드 컨벤션
- 깃 컨벤션
- 패키지 컨벤션

#### Entity 매핑
- **여기 어노테이션이 무엇에 사용되는지는 알고있기**
> - `@Entity`: 이 클래스가 JPA의 엔티티임을 의미
> - `@Table`: DB의 테이블을 정의
> - `@Id`: DB의 PK 부분을 의미
> - `@GeneratedValue`: 생성 전략을 선택, IDENTITY인 경우, 순차적 생성 (1 → 2 → …)
> - `@Column`: DB의 속성 부분을 의미
> - `@Enumerated` : Enum을 사용할 때, 데이터의 형태를 명시화
> - `@NoArgsConstructor`  :  기본 생성자를 자동으로 생성합니다. JPA 엔티티는 내부적으로 프록시 생성을 위해 반드시 기본 생성자를 필요로 합니다. 다만 개발자가 직접 호출하는 실수를 막기 위해 access = AccessLevel.PROTECTED 로 두는 것이 권장됩니다.
> - `@Builder.Default` : 초기값 지정
> - `@EntityListeners(AuditingEntityListener.class)`을 앤티티 위에 작성 후 자동 시간입력이 필요한 어노테이션 속성 위에 `@CreatedDate, @LastModifiedDate` 작성 그 이후 클래스에 `@EnableJpaAuditing`

#### BaseEntity 설정
- 엔티티에 중복된 설정이 있을 경우 사용
- BaseEntity에 공통적인 속성들을 정의하고 상속 시켜줌

#### 연관 관계 지정
---

- # RESTful API Endpoint의 설계
### API Endpoint
- API가 두 시스템이 상호작용하게 해주는 인터페이스라면, ENDPOINT는 API가 서버에서 리소스에 접근할 수 있게 해주는 URL이라고 할 수 있다.
- URI: 하나의 자원을 고유하게 식별하는 문자열을 의미 / URL: 자원의 위치를 식별하는 문자열 / URN: 자원의 고유한 이름을 식별하는 문자열
- REST API의  Endpoint 규칙
>1. URI에 **동사가 포함이 되어선 안된다.**
>2. URI에서 **단어의 구분이 필요한 경우 -(하이픈)을 이용**한다.
>3. **자원**은 기본적으로 **복수형으로 표현**한다.
>4. 단 하나의 자원을 **명시적으로 표현**을 하기 위해서는 **/users/id와 같이 식별 값을 추가로 사용**한다.
>5. **자원 간 연관 관계가 있을 경우 이를 URI에 표현한다.**

#### 연관 관계 지정
- `FetchType.LAZY` 지연 로딩
- `@OneToOne(mappedBy = “reply")`1:1관계
- 양방향 매핑
  - **casecade**
   > - ALL: 모든 cascade 작업 전파
   > - DETACH: 엔티티 분리 시, 연관 엔티티도 분리
   > - MERGE: 엔티티 병합 시, 연관 엔티티도 병합
   > - PERSIST: 엔티티 저장 시, 연관 엔티티도 저장
   > - REFRESH: 엔티티 새로고침 시, 연관 엔티티도 새로고침
   > - REMOVE: 엔티티 삭제 시, 연관 엔티티도 삭제
---
# 핵심 키워드
1. #### 계층형 구조 vs 도메인형 구조
   - 계층형 구조 : 역할 별로 나눈다(Controller, Service, Repository)
   - 도메인형 : 기능 또는 업무 영역(도메인 별로 나누어서 저장)
2. #### JPA
   - JPA는 자바에서 사용하는 객체와 데이터베이스(RDB)에서 발생하는 패러다임 불일치를 해결하기 위한 ORM을 표준을 의미한다.
   - 애플리케이션의 객체를 RDB 테이블에 자동으로 영속화 해주는 것
   - 요즘 많이 사용하는 구현체 Hibernate가 있다.
3. #### N+1 문제
   - 이 문제는 위에서 말한 JPA문제 떄문에 발생한다
   - 객체는 연관관계를 통해 래퍼런스를 가지고 있으면 언제든지 Random Access를 통해 연관 객체 접근할 수 있지만 EDB의 경우 Select 쿼리를 통해서만 조회할 수 있기 때문
   - Fetch Join + Lazy Loading
     - Fetch Join은 Root Entity에 대해서 조회 할 때 Lazy Loading 으로 설정 되어있는 연관관계를 Join쿼리를 발생시켜 한번에 조회할 수 있는 기능
4. #### 기본 키 생성 전략
   - IDENTITY | DB가 자동 생성
   - SEQUENCE | DB의 시퀀스를 사용해 PK 생성
   - TABLE | 별도의 생성용 테이블을 만들어 PK 관리
   - AUTO | JPA가 DB에 맞게 자동 선택

---

# 학습 후기
- 평소 알고 있다고 생각을 했지만 막상 이렇게 보니 너무 모르는 것이 많다는 것을 꺠달았다
- 이번 기회로 N+1의 관계에서 생기는 문제점을 알 수 있게 되었고 적용을 하는 법을 찬찬히 더 자세히 알아야 되겠다는 생각을 하였다
- 백앤드는 그저 데이터를 보내주기만 하면 될 줄 알았는데 데이터관리법 프로젝트 구조를 배우니 새삼 내가 배우고 있는 길이 생각보다 어렵다는 것을 꺠달았다
---
# 미션
### 1주차 사진
 <img alt="erd0" src="https://github.com/mybookG/image/blob/main/erd0.png?raw=true" />

### github 링크
https://github.com/mybookG/Umc_personal_repository/tree/main/UMC_4%EC%A3%BC%EC%B0%A8%EB%AF%B8%EC%85%98