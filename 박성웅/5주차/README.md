# Chapter 5. JPA 활용
## 학습 목표
- 즉시 로딩과 지연 로딩의 전략 차이에 대해 알아보고, 지연 로딩을 채택하는 이유에 대해 이해한다.
- JPQL과 QueryDSL의 차이에 대해 이해한다.

## 영속성 컨텍스트란
- JPA가 관리하는 메모리상의 논리적 공간
- DB랑 앱 사이에서 엔티티 객체를 중간 캐시처럼 유지하는 가상의 데이터베이스 역할
- 엔티티 객체를 영구적으로 관리(저장,추적,동기화)
- EntityManager를 통해 접근

## 엔티티의 생명주기
- 비영속(new/transient):아직 영속성 컨텍스트에 관리되지 않은 상태
- 영속 (managed):영속성 컨텍스트에서 관리 중인 상태
- 준영속 (detached):한 번 관리되었다가 분리된 상태
- 삭제 (removed):삭제 명령이 수행된 상태

## 영속 상태의 장점
### 1. 1차 캐시
동일 트랜잭션 내에서 동일한 엔티티를 재조회할 때 DB 접근 없이 캐시에서 반환
트랜잭션이 종료될 때까지 엔티티 객체가 유지되어 성능 향상

### 2. 변경 감지 (Dirty Checking):
- 엔티티의 변경 사항을 자동으로 감지
- commit() 시 내부적으로 flush() 가 호출되며 변경된 필드만 DB에 자동 Update

### 3. 지연 로딩 (Lazy Loading):
- 연관된 엔티티를 실제 사용하는 시점에 조회
- 전체를 한 번에 가져오는 즉시 로딩(EAGER) 과 달리
- 지연 로딩(LAZY) 은 필요할 때만 쿼리를 발생시켜 효율적

## N+1 문제
- 즉시 로딩(EAGER)을 사용할 때 발생
- 하나의 엔티티를 조회하면서 연관된 N개의 엔티티를 각각 추가로 조회
- `SELECT * FROM member;` 이후 각 member마다
- `SELECT * FROM member_food WHERE member_id = ?;` 실행
- 지연 로딩(LAZY)을 활용하여 필요한 시점에만 연관 데이터를 조회함으로써 성능을 개선

## JPQL (Java Persistence Query Language)
- SQL과 비슷하지만 객체(Entity) 를 대상으로 쿼리를 작성
- 데이터베이스 테이블이 아닌 엔티티 클래스와 필드명을 사용

```java
@Query("select m from Member m where m.name = :name and m.deletedAt is null")
List<Member> findActiveMember(@Param("name") String name);
```
- `@Query` 어노테이션을 통해 직접 JPQL을 작성하거나, `nativeQuery = true` 옵션으로 SQL 실행도 가능

## Repository 기반 쿼리 생성
- Spring Data JPA는 메서드 이름 규칙만으로 쿼리를 자동 생성합니다.
```java
List<Member> findByNameAndDeletedAtIsNull(String name);
```
내부적으로 `SELECT * FROM member WHERE name = ? AND deleted_at IS NULL` 쿼리 자동 실행

## QueryDSL
- 타입 안전한 쿼리 빌더 라이브러리
- 복잡하거나 동적 쿼리를 작성할 때 유용
- 런타임이 아닌 컴파일 시점에 오류를 잡을 수 있음
> 검색 조건이 여러 개(가게명,지역명,상세주소 등)일 때
> if 조건문 없이 깔끔하게 조건을 조합


# 학습 후기
- QueryDSL로 조건을 조합하면서 동적 쿼리가 깔끔해지는 것에 만족했다.

# 미션
- https://github.com/sungw00ng/UMC-week-mission
