# QueryDSL

- 문자열로 쿼리를 작성할 시, 컴파일 타임에 오류를 잡기가 힘들다.

## Fluent API

- Fluent Interface 패턴을 실제 API 설계에 적용한 것을 말한다.
- Fluent Interface는 디자인 패턴으로, 메서드 체이닝을 활용해 코드의 흐름을 자연스럽고 읽기 쉽게 만드는 패턴이다.(빌더 패턴?)

> Fluent API를 사용할 시 장점
> 1. IDE의 코드 자동 완성 기능 사용
> 2. 문법적으로 잘못된 쿼리를 허용하지 않음
> 3. 도메인 타입과 프로퍼티를 안전하게 참조할 수 있음
> 4. 도메인 타입의 리팩터링을 더 잘 할 수 있음

## 동적 쿼리란?

- 런타임에 입력에 따라 조건을 동적으로 구성하여 SQL을 생성, 실행하는 쿼리 기법

## QClass란?

- 쿼리 전용 클래스를 만드는 과정: 객체 + 메타 데이터
- 엔티티 클래스를 기반으로 생성되는 쿼리 전용 클래

> QClass를 사용할 시 장점
> 1. 타입 안정성: 컴파일 타임에 엔티티 기반으로 쿼리를 생성하므로, 런타임에 오류를 예방할 수 있다.
> 2. 자동 완성: 속성 이름을 외울 필요 없이, IDE 자동 완성 기능을 활용해 쿼리를 작성할 수 있다.
> 3. Fluent Interface 스타일: 메서드 체이닝 기법을 활용하기 때문에 자연스럽고 읽기 쉬운 쿼리를 작성할 수 있다.
> 4. 내부 DSL 효과: SQL/JPQL 문법을 자바 코드 안에서 도메인 특화 언어처럼 표현할 수 있다.

## QClass는 깃허브에 올리면 안된다.

- Q클래스를 최상단에 두고 .gitignore 하자.

# QueryDSL의 몰락

- 기존 QueryDSL 프로젝트는 마지막 릴리즈가 2024년이다.
- OpenFeign 팀이 레포지토리를 Fork 하여 심폐소생술하는 중이다.

# 핵심 키워드
- QueryDSL에서 FetchJoin 하는 법
```java
List<Member> members = queryFactory
    .selectFrom(member)
    .join(member.team, team).fetchJoin()
    .where(team.name.eq("백엔드팀"))
    .fetch();
```

- DTO 매핑 방식 (+DTO안에 DTO)
```java
List<MemberDto> result = queryFactory
    .select(Projections.constructor(MemberDto.class,
        member.username,
        Projections.constructor(TeamDto.class, team.name)
    ))
    .from(member)
    .join(member.team, team)
    .fetch();
```

- 커스텀 페이지네이션
```java
List<MemberDto> content = queryFactory
    .select(new QMemberDto(member.username, team.name))
    .from(member)
    .leftJoin(member.team, team)
    .offset(pageable.getOffset())
    .limit(pageable.getPageSize())
    .fetch();

long total = queryFactory
    .select(member.count())
    .from(member)
    .fetchOne();

return new PageImpl<>(content, pageable, total);
```

- transform - groupBy
  - 1:N 관계에서 하나의 부모에 여러 자식 데이터를 매핑하기 위한 QueryDSL의 집계 도구.
  - SQL의 group by가 아니라, Java 메모리 내에서의 그룹핑 변환이다.
```java 
Map<Long, MemberGroupDto> result = queryFactory
  .from(member)
  .join(member.team, team)
  .transform(
      groupBy(team.id).as(
          new QMemberGroupDto(
              team.name,
              list(new QMemberDto(member.username))
          )
      )
  );

List<MemberGroupDto> groups = new ArrayList<>(result.values());
```

- order by null
  - MySQL에서는 ORDER BY NULL을 사용해 정렬 연산을 완전히 제거할 수 있다.
```java
queryFactory
    .select(member.username)
    .from(member)
    .orderBy(Expressions.stringTemplate("null").asc())
    .fetch();
```

# 후기
- QueryDSL에 대해 파악하지 못했던 부분을 많이 알게 되었다.
- QueryDSL이 사실상 유지보수가 중단된 상태라 아쉽다는 생각이 들었고, 오픈소스 프로젝트가 지속되기 위해서는 커뮤니티의 관심과 기여가 중요하다는 점을 다시금 깨달았다.

# 미션

링크 - [6주차 미션 PR 링크](https://github.com/chltjsdl0119/UMC-chapter4-mission/pull/1)