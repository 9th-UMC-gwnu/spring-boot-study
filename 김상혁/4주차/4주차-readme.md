# JPA 기초
## 객체지향 언어와 관계형 DB 패러다임의 불일치 
- **객체 지향적 언어인 자바**는 궁극적으로 **캡슐화/상속/다형성을 활용하는 것이 목표**이고,<br> 
**RDBMS(관계형 데이터베이스)**는 **데이터를 정교하게 구성하는 것이 목표**<br>서로 이루고자 하는 바가 다르기에 조합이 완전히 맞지 않는다
- 데이터를 다룰 때, RDB에 저장된 정보를 조회하여 자바 객체로 변환하고, 수정이나 삭제를 할 때는 변경된 객체의 상태를 다시 SQL로 변환해야 한다

## JPA란?
- JPA(Java Persistence API)란, 자바 진영에서 ORM(Object-Relational Mapping) 기술의 표준으로 사용되는 인터페이스의 모음
> ORM(Object Relational Mapping)이란?
> - 애플리케이션의 클래스와 RDB의 테이블을 매핑하는 기술
> - 반복적인 CRUD SQL 쿼리문을 자동으로 매핑하여 날려준다
> - 대표적인 오픈소스로는 Hibernate가 존재

# Spring 프로젝트 설계 및 구조
## 대표적인 프로젝트 구조
- 파사드 패턴 구조
- 도메인형 구조
- 계층형 구조
- 헥사고날 구조
## 어노테이션 종류
- `@Entity`: 이 클래스가 JPA의 엔티티임을 의미
- `@Table`: DB의 테이블을 정의
- `@Id`: DB의 PK 부분을 의미
- `@GeneratedValue`: 생성 전략을 선택, IDENTITY인 경우, 순차적 생성 (1 → 2 → …)
- `@Column`: DB의 속성 부분을 의미
- `@Enumerated` : Enum을 사용할 때, 데이터의 형태를 명시화
- `@NoArgsConstructor`  :  기본 생성자를 자동으로 생성합니다. JPA 엔티티는 내부적으로 프록시 생성을 위해 반드시 기본 생성자를 필요로 합니다. 다만 개발자가 직접 호출하는 실수를 막기 위해 access = AccessLevel.PROTECTED 로 두는 것이 권장됩니다.

## 세부 설정
- @Column에서 nullable = boolean, length = int 로 not null과 글자수 제한
- @builder.Default 어노테이션을 붙히고 초기값 지정
- 속성위에 @CreatedDate, @LastModifiedDate 어노테이션을 적고 엔티티 위에 @EntitiyListeners(AuditingEntityListener.class) 작성 후
  어플리케이션 클래스에 @EnableJpaAuditing 어노테이션 적으면 생성, 수정 시간을 자동으로 입력
  
## 연관 관계 지정
- @ManyToOne : 1:N 관계에서 이 엔티티가 1 임을 정의
- @JoinColumn : FK의 주인 설정
- 양방향 매핑을 위해서는 @OneToOne(1:1 연관관계 설정), @OneToMany(1:N에서 이 엔티티가 N임을 정의) 사용
## cascade
- cascade란, 어느 한 테이블을 수정, 삭제할 경우 연관된 모든 테이블을 함께 수정, 삭제하는 기능
- 양방향 매핑을 통해 cascade 설정이 가능
> cascade 기능
> - ALL: 모든 cascade 작업 전파
> - DETACH: 엔티티 분리 시, 연관 엔티티도 분리
> - MERGE: 엔티티 병합 시, 연관 엔티티도 병합
> - PERSIST: 엔티티 저장 시, 연관 엔티티도 저장
> - REFRESH: 엔티티 새로고침 시, 연관 엔티티도 새로고침
> - REMOVE: 엔티티 삭제 시, 연관 엔티티도 삭제

# 핵심 키워드
- 계층형 구조 vs 도메인형 구조
  - 계층형 구조 : controller → service → repository 의 구조
    - 계층이 명확해서 초반 설계가 단순
    - 기능이 많아질수록 폴더 간 이동이 많음
  - 도메인형 구조 : domain 중심으로 기능별 묶음
     - 도메인 단위로 기능이 모여 있어서 유지보수와 확장이 편리
     - 초반 설계가 조금 복잡하고, 도메인 경계를 잘 나눠야 한다
- JPA : 자바 객체를 DB 테이블에 매핑해주는 ORM 기술의 표준으로 객체 지향과 RDB간의 패러다임 불일치를 해결
- N+1 문제 : 데이터를 조회할 때 연관된 데이터를 N번 추가 조회하는 문제
   - JPA의 지연 로딩(LAZY) 떄문에 연관된 엔티티를 필요할 떄마다 쿼리로 부름
   - Fetch Join, EntitiyGraph, 즉시로딩인 EAGER(권장 X)로 해결 가능
- 기본 키 생성 전략 : JPA는 @GeneratedValue를 이용해 엔티티의 PK를 자동 생성할 수 있게 해준다
  - IDENTITY : DB의 AUTO_INCREMENT 사용
  - SEQUENCE : DB 시퀀스 객체 사용
  - TABLE : 별도의 키 관리용 테이블 생성
  - AUTO : DB 종류에 따라 자동 선택

