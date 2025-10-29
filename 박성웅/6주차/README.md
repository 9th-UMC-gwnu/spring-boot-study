## Chapter 6. QueryDSL 시작

목차
---
* QueryDSL이란
  * JPA와의 차이점
  * 철학과 특징
* 동적 쿼리 구성
  * BooleanBuilder와 Predicate
  * 체이닝과 조건 조합
* Q 클래스
  * 생성 구조
  * 커밋 제외 이유

---

QueryDSL이란
---
QueryDSL은 SQL이나 JPQL을 문자열이 아닌 코드로 표현하기 위한 타입 안전 쿼리 라이브러리다.
쿼리를 “작성”하는 대신 객체를 조립하듯 설계하며, 실행 전 컴파일 단계에서 오류를 검증할 수 있다.

JPA와의 차이점
---
JPQL은 문자열 기반으로 작성되어 IDE 지원이 미흡하고, 오류가 실행 시점에 드러난다.
반면, QueryDSL은 정적 타입 시스템으로 문법을 검증하고 자동 완성 지원을 적극 활용할 수 있다.

특징
---
QueryDSL의 목적은 단순한 쿼리 빌더가 아니라 엔티티 중심의 질의 언어를 구현하는 것이다.
즉, 데이터베이스 구조가 아닌 도메인 모델(엔티티) 중심으로 데이터 접근을 표현한다.

---

동적 쿼리 구성
---
비즈니스 로직은 입력 조건에 따라 필터가 달라진다.
QueryDSL은 조건을 유연하게 추가·조합할 수 있도록 BooleanBuilder와 Predicate를 제공한다.

BooleanBuilder와 Predicate
---
- BooleanBuilder: 여러 조건을 누적하여 where 구문을 동적으로 완성한다.
- Predicate: BooleanBuilder로 생성된 조건식으로, 실제 where 절의 논리 표현이다.

예시 코드
```java
BooleanBuilder builder = new BooleanBuilder();
if (userName != null) builder.and(user.name.eq(userName));
if (age != null) builder.and(user.age.gt(age));
return queryFactory.selectFrom(user).where(builder).fetch();
```

조건 존재 여부에 따라 쿼리 구조가 달라져도 SQL 문자열을 직접 수정하지 않아도 된다.

체이닝과 조건 조합
---
and, or, not 연산자를 체이닝하여 가독성과 유연성을 높인다.

예시 코드
```java
queryFactory
    .selectFrom(post)
    .where(post.title.contains(keyword)
           .and(post.status.eq(Status.PUBLISHED)))
    .fetch();
```
---

Q 클래스
---
Q 클래스는 컴파일 시점에 생성되는 엔티티 전용 메타모델로, 각 필드를 타입 안전하게 참조할 수 있다.
예를 들어 User 엔티티가 있을 때 QUser 클래스가 자동 생성된다.

생성 구조
---
Annotation Processing Tool(APT)이 @Entity 대상 클래스를 분석해 Q 클래스를 만든다.

예시 코드
```java
QUser user = QUser.user;
queryFactory
    .select(user.name, user.age)
    .from(user)
    .where(user.age.gt(20))
    .fetch();
```
필드는 문자열이 아닌 객체로 접근하므로, 리팩터링 시 쿼리의 안전성이 유지된다.

커밋 제외 이유
---
Q 클래스는 빌드 시 자동 생성되며, 환경마다 내용이 달라질 수 있다.
따라서 build/generated/querydsl 폴더를 .gitignore로 제외해 충돌을 방지해야 한다.

---

후기
---
QueryDSL은 “쿼리도 도메인의 일부”라는 관점으로 접근하는 언어인 것 같았다.

## 미션
- https://github.com/sungw00ng/UMC-week-mission/tree/FEAT/Chapter6

