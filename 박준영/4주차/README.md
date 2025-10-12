# 객체 지향 언어와 관계형 데이터베이스

**객체 지향 언어인 자바는** 캡슐화/다형성/상속을 활용하는 것이 목표
**관계형 데이터베이스는** 데이터를 정교하게 구성하는 것이 목표
-> 결국 관계형 데이터베이스에 저장된 데이터를 조회하여 자바 객체로 변환하거나 수정, 삽입시엔 다시 SQL 상태로 변환하여 RDBMS 에 저장해야한다.

## 패러다임 불일치

- RDB 는 하나의 조인키만으로도 테이블에서 양방향 탐색 가능, 객체 지향에선 연관관계의 주인을 명시해야 한다.
- 객체 지향은 상속이 기본 개념이지만, RDB 에선 상속 개념이 없다.
- DB는 DATE, NUMBER 같은 스키마를 사용하지만 자바는 LocalDateTime, enum 등을 사용
-> 관계형 데이터베이스와 객체 지향 사이엔 이러한 패러다임 불일치가 발생


# JPA

- 자바 진영에서 **ORM(Object-Relational Mapping)** 기술의 표준으로 사용되는 인터페이스의 모음 

## ORM

- 어플리케이션의 클래스와 RDB의 테이블을 매핑해주는 기술
  - 반복적인 CRUD SQL 쿼리문 없이 자동으로 매핑
  - 대표적인 오픈소스로는 Hibernate


# Spring 프로젝트 설계 및 구조
- 파사드 패턴 구조
  - 복잡한 로직을 하나로 묶은 인터페이스(Facade)를 제공하여 클라이언트 코드를 단순화
  - Facade 자체가 비대해지면 유지보수가 어려워지고 디버깅이 어려워짐
- 도메인형 구조
  - 도메인 변경이 다른 모듈에 영향을 끼치지 않으며, 테스트가 용이
  - 프로젝트 규모가 작으면 오히려 구조가 복잡해질 수 있음
- 계층형 구조
  - 구조가 명확하여 이해하기 쉬움
  - 계층이 깊어질수록 유연성이 떨어짐
- 헥사고날 구조
  - 비즈니스 로직은 외부에 의존하지 않고 외부와의 의존성을 **Port(인터페이스)**와 **Adapter**클래스를 통해 연결
  - 유연성이 높고, 테스트와 유지보수 최적화
  - 초기 설계 복잡하고 작은 규모에는 과도


# Entity 매핑

- @Entity : 이 클래스가 JPA의 엔티티임을 의미
- @Table : DB의 테이블을 정의
- @Id : DB의 PK 부분을 의미
- @GeneratedValue : 생성 전략을 선택, IDENTITY인 경우, 순차적 생성 (1 → 2 → …)
- @Column : DB의 속성 부분을 의미
- @Enumerated : Enum을 사용할 때, 데이터의 형태를 명시화
- @NoArgsConstructor :  기본 생성자를 자동으로 생성
  - JPA 엔티티는 내부적으로 프록시 생성을 위해 반드시 기본 생성자를 필요 
  - 다만 개발자가 직접 호출하는 실수를 막기 위해 access = AccessLevel.PROTECTED 로 두는 것이 권장

# CASCADE 기능

- 어느 한 테이블을 수정, 삭제할 경우 연관된 모든 테이블을 함께 수정, 삭제하는 기능
  - ALL: 모든 cascade 작업 전파
  - DETACH: 엔티티 분리 시, 연관 엔티티도 분리
  - MERGE: 엔티티 병합 시, 연관 엔티티도 병합
  - PERSIST: 엔티티 저장 시, 연관 엔티티도 저장
  - REFRESH: 엔티티 새로고침 시, 연관 엔티티도 새로고침
  - REMOVE: 엔티티 삭제 시, 연관 엔티티도 삭제


# 핵심 키워드

- 계층형 구조 vs 도메인형 구조

  계층형 구조는 계층별로 구분하여 프로젝트 구조를 구성하는 것으로 역할을 기준으로 패키지가 나뉜다.
  명확한 책임분리에 용이하지만 프로젝트 규모가 커지면 도메인이 섞이며 복잡해진다.

  도메인형 구조는 도메인별로 즉, 기능별로 구분하여 프로젝트 구조를 구성하는 것으로 기능단위로 코드가 모여있어 확장성이 좋지만 소규모 프로젝트에선 구조가 복잡해질 수 있다.

- JPA

  **자바 진영에서 ORM(Object-Relational Mapping)** 기술의 표준으로 사용되는 인터페이스의 모음

- N+1 문제

  한번의 쿼리문으로 끝날 줄 알았는데 N번의 추가 쿼리가 실행되는 문제

  EX)

    List<Member> members = em.createQuery("SELECT m FROM Member m", Member.class)
    .getResultList();

    for (Member m : members) {
      System.out.println(m.getTeam().getName());
    }

  이런 식으로 하게 되면 SELECT * FROM Member; 이 1번 실행되고 
  이후에 for문 내부에서 getTeam()으로 JPA에서 SELECT * FROM team WHERE id = ?; 를 N번 실행하게 된다.

  "SELECT m FROM Member m"를 FETCH JOIN을 사용하여
  “SELECT m FROM Member m JOIN FETCH m.team” 로 수정하면 해결할 수 있다.

- 기본 키 생성 전략

  - JPA의 @GeneratedValue로 PK 생성 방식을 지정
    - **IDENTITY** : DB의 AUTO_INCREMENT 사용
    - **SEQUENCE** : 시퀀스 객체를 통해 식별자 생성
    - **TABLE** : 별도의 테이블로 시퀀스 흉내냄
    - **AUTO** : DB 방언(Dialect)에 따라 자동 선택


--- 

# 학습 후기

- 프로젝트 구조에서 항상 갈팡질팡하였는데 도메인 중심, 계층 중심 등의 다양한 방식이 있음을 알아 도움이 되었다.
- N+1 문제라는 것을 알아감으로써 Getter를 사용할 때도 신중하게 써야하겠다는 것을 익힐 수 있었다.


---

# 미션

[GitHub - nemm3623/UMC-chapter4-mission] (https://github.com/nemm3623/UMC-chapter4-mission)