Chapter 6. QueryDSL 시작
===
목차
---
* QueryDSL이란
  * JPA와의 차이점
  * 장점과 철학
* 동적 쿼리 생성
  * BooleanBuilder와 Predicate
  * 조건 조합과 체이닝
* Q 클래스란
  * 생성 원리와 구조
  * 커밋 금지 이유

---

### QueryDSL이란
QueryDSL은 **타입 안전한 쿼리 생성 라이브러리**로, SQL이나 JPQL을 문자열 기반으로 작성하는 불편함을 해소하고자 등장했다.  
SQL을 코드로 표현할 수 있게 하여, **컴파일 시점에서 쿼리의 오류를 잡아낼 수 있다는 점**이 가장 큰 특징이다.

JPQL이 문자열 기반이라면, QueryDSL은 메서드 체이닝을 통해 쿼리의 각 구문을 **객체로 구성**한다.  
즉, 쿼리를 직접 ‘작성’하는 것이 아니라, 쿼리 문장을 ‘조립’하는 것이다.

#### JPA와의 차이점
JPQL은 실행 전까지 문법 오류를 잡을 수 없으며, 문자열로 쿼리를 정의하기 때문에 리팩터링이나 IDE 지원이 약하다.  
반면 QueryDSL은 정적 타입 기반으로 동작하여, 컴파일 단계에서 문법 검증이 이루어지고, IDE의 자동 완성 기능을 적극적으로 활용할 수 있다.

#### 장점과 철학
QueryDSL의 철학은 단순히 쿼리 빌더를 제공하는 것이 아니라, **도메인 중심의 데이터 질의(Domain-driven Querying)**를 구현하는 데 있다.  
즉, 데이터베이스의 테이블이 아니라 애플리케이션의 ‘엔티티 구조’를 중심으로 질의를 정의한다는 점이 핵심이다.

---

### 동적 쿼리 생성
현실의 비즈니스 로직은 단일한 조건이 아닌, **상황에 따라 달라지는 필터링 조건**을 가진다.  
이를 위해 QueryDSL은 BooleanBuilder 또는 Predicate를 사용하여 동적으로 조건을 조립할 수 있다.

#### BooleanBuilder와 Predicate
- **BooleanBuilder**는 쿼리의 조건문을 누적시킬 수 있는 객체이다.  
  예를 들어, 검색 조건이 존재하는 경우에만 `builder.and()`를 추가해가며 조건을 점진적으로 완성할 수 있다.  
- **Predicate**는 `where()` 절 내부에서 평가되는 불리언 표현식으로,  
  BooleanBuilder로 생성된 결과가 Predicate로 반환되어 실제 쿼리에 적용된다.

예시:
```java
BooleanBuilder builder = new BooleanBuilder();
if (userName != null) {
    builder.and(user.name.eq(userName));
}
if (age != null) {
    builder.and(user.age.gt(age));
}
return queryFactory.selectFrom(user).where(builder).fetch();
````
이처럼 조건의 존재 여부에 따라 쿼리의 구조가 실시간으로 달라지며, SQL 문자열을 직접 수정할 필요가 없다.

#### 조건 조합과 체이닝

QueryDSL의 조건문은 논리 연산자(and, or, not)로 연결할 수 있으며,
체이닝 형태로 이어붙여 읽기 좋은 쿼리 구조를 만든다.

``` java
queryFactory
    .selectFrom(post)
    .where(post.title.contains(keyword)
           .and(post.status.eq(Status.PUBLISHED)))
    .fetch();
```
### Q 클래스란

Q 클래스는 QueryDSL이 컴파일 단계에서 생성하는 엔티티 전용 메타모델 클래스이다.
각 엔티티마다 대응되는 Q 클래스가 만들어지며, 클래스명은 Q엔티티명 형식을 따른다.
예를 들어 User 엔티티가 있다면, QUser 클래스가 자동 생성된다.

#### 생성 원리와 구조

Q 클래스는 @Entity가 선언된 엔티티를 분석하여 apt(Annotation Processing Tool)를 통해 생성된다.
이 클래스는 각 엔티티의 컬럼을 타입 안전한 필드로 표현하며,
쿼리 작성 시 IDE의 자동 완성 기능으로 활용된다.

``` java
QUser user = QUser.user;
queryFactory
    .select(user.name, user.age)
    .from(user)
    .where(user.age.gt(20))
    .fetch();
```

여기서 user.age와 같은 필드 접근은 문자열이 아닌 객체 참조로 이루어진다.
이는 코드 리팩터링이나 이름 변경 시에도 쿼리의 안전성을 보장한다.

#### 커밋 금지 이유

Q 클래스는 빌드 시점에 자동 생성되는 파일로, 사람이 직접 수정할 필요가 없으며
프로젝트를 클린 빌드하면 언제든 다시 생성된다.
따라서 이 파일을 Git에 커밋하면 팀원별 환경 차이로 충돌이 발생할 수 있고,
이는 유지보수성을 해친다.
즉, build/generated/querydsl 경로의 Q 클래스들은 .gitignore로 제외해야 한다.

### 후기

QueryDSL은 단순히 JPQL을 대체하는 도구가 아니라, 엔티티 중심의 질의 언어라는 관점에서 훨씬 더 큰 의미를 가진다.
정적 타입 시스템과 객체 지향 쿼리 구성을 결합함으로써, 쿼리를 더 안전하게 설계할 수 있으며,
이는 결국 ‘쿼리도 도메인 모델의 일부’라는 사고로 이어진다.

### 미션 링크
[6주차 미션 브랜치 링크](https://github.com/hexter31376/umc_mission4/tree/feat/Chapter6)