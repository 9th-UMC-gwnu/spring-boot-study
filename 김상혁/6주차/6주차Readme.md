# Query DSL
- 정적 타입을 이용해 SQL과 같은 쿼리를 생성할 수 있도록 지원하는 프레임워크
- 문자열이나 XML 파일을 통해 쿼리를 작성하는 대신 플루언트(Fluent) API를 활용해 쿼리를 생성
## Fluent API, DSL, QueryDSL
### Fluent API
- Fluent API는 Fluent Interface 패턴을 실제 API 설계에 적용한 것
- 프레임워크나 라이브러리에서 Fluent Interface 개념을 코드로 제공한 것
- Fluent Interface는 디자인 패턴으로, 메서드 체이닝을 활용해 코드의 흐름을 자연스럽고 읽기 쉽게 만드는 패턴
- Fluent 스타일은 그 의도가 내부 도메인 특화 언어(Internal Domain-Specific Language, DSL)와 유사하게 동작하도록 설계
- 이 API는 무엇보다 읽기 쉽고 자연스럽게 흐르는(flow) 코드를 만들기 위해 설계
### QueryDSL
- QueryDSL은 이 Fluent Interface/Fluent API 개념을 적용하여, 복잡한 JPQL/SQL 쿼리를 자바 코드 안에서 자연스럽게 구성할 수 있게 해줌
- Fluent 스타일을 활용한 QueryDSL 코드는 내부 DSL(Internal Domain-Specific Language)의 형태를 띠게 되는데, 여기서 DSL의 의미가 QueryDSL에서 말하는 ‘DSL’과 동일하게, 특정 도메인(데이터베이스 쿼리)에 특화된 언어라는 점이 연결

> 단순 문자열과 비교해서Fluent API를 사용할 때의 장점
> - IDE의 코드 자동 완성 기능 사용
> - 문법적으로 잘못된 쿼리를 허용하지 않음
> - 도메인 타입과 프로퍼티를 안전하게 참조할 수 있음
> - 도메인 타입의 리팩토링을 더 잘 할 수 있음

- QueryDSL에서는 BooleanBuilder, BooleanExpression, Predicate 등과 같은 객체와 인터페이스를 제공하여, 동적 쿼리를 안전하게 구성
- JPQL에선 런타임에서 잡는걸, QueryDSL은 컴파일에서 잡아줌.즉, IDE에서 오류를 캐치
## 동적 쿼리란?
- 런타임에 입력(사용자 필터, API 파라미터 등)에 따라 WHERE / JOIN / ORDER BY 등의 조건을 동적으로 구성해 SQL을 생성, 실행하는 기법
- 실행 시점에 쿼리 문자열(또는 쿼리 객체)을 만들어 DB에 전달하고 실행
- 정적 쿼리는 쿼리 구조가 고정되어 있어 필터의 개수·조합에 유연하게 반응하기 어렵지만, 동적 쿼리는 조건을 붙였다 떼었다 하며 런타임에 맞춰 결과를 반환
- 다만 사용자 입력을 그대로 문자열에 결합하면 SQL 인젝션에 취약

## Q클래스란?
- QueryDSL은 코드 기반으로 생성하는데 코드를 작성하면서 타입 안정성과 객체를 기반으로 작성
- QueryDSL에서 Q클래스는 엔티티(Entity)를 기반으로 생성되는 쿼리 전용 클래스입니다.
쉽게 말해, 데이터베이스 쿼리를 Fluent 스타일의 내부 DSL로 안전하게 작성할 수 있도록 지원하는 “타입 안전 쿼리 객체”입니다.

> Q클래스를 사용하면서 챙기는 이점
> - 타입 안정성: 컴파일 단계에서 엔티티 기반으로 쿼리를 생성하므로, 잘못된 필드나 타입 사용 시 에러 발생 → 런타임 오류 예방
> - 자동완성: 속성 이름을 외울 필요 없이 IDE 자동완성 기능을 활용해 쿼리를 작성 가능 → 개발 효율 상승
> - Fluent Interface 스타일: 메서드 체인을 활용해 자연스럽고 읽기 쉬운 쿼리 작성 가능 → 코드 가독성 향상
> - 내부 DSL(Internal DSL) 효과: SQL/JPQL 문법을 자바 코드 안에서 도메인 특화 언어처럼 표현 가능 → 의도 직관적

### Q클래스와 깃허브
- Q클래스를 깃허브에 올리면 예기치 않은 오류가 발생
- Q클래스는 객체 구조가 변경될 때마다, 자바 버전마다 다르게 생성
- 공유하지 않는 방법 : Q클래스를 최상단에 두고 .gitignore 처리

## 기존 QueryDSL의 몰락
- 기존 QueryDSL에서 CVE 취약점이 발견됬으나 QueryDSL에서 별다른 지원이 없음
- 여러 팀이 QueryDSL을 살리기 위해 Fork하며 유지보수 중이고 대표적으로 OpenFeign팀의 QueryDSL이 있다.

# JPARepository 작성법
- QueryDSL을 사용하는 방법
  - Q클래스를 정의
  - 코드로 쿼리문 작성
  - BooleanBuilder로 Where문 작성
- QueryDSL 관련 쿼리를 인터페이스화, 구현해서 사용 (JPA에서 제공하는 기본 메서드 (save, findById 등) 을 사용 가능)

# 핵심 키워드
- QueryDSL에서 FetchJoin 하는법
- DTO 매핑 방식(+DTO 안에 DTO)
- 커스텀 페이지네이션
- transform - groupBy
- order by null


















