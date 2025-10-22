# Spring Data JPA 영속성 컨텍스트
## 영속성 컨텍스트(Persistence Context)란?
- 데이터(엔티티 객체)를 영구적(persistent)으로 저장할 엔티티 객체를 관리하고, 변경 내용을 추적하는 메모리 상의 논리적 공간
- @EntityManager를 이용하여 접근
## 영속성이란?
- 에플리케이션 단의 객체를 엔티티 매니저가 계속 관리를 하면서, 변경 내용을 추적한다. 이때, 엔티티 메니저가 관리한다는 개념이 영속성
- 엔티티 메니저의 영속화에 따른 객체의 생명주기
  - 비영속 (new/transient) : 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태
  - 영속 (managed) : 영속성 컨텍스트에 관리되는 상태
  - 준영속 (detached) : 영속성 컨텍스트에 저장되었다가 분리된 상태
  - 삭제 (removed) : 삭제된 상태
  ## 영속상태의 장점
  1. 1차 캐시
    -  영속성 컨텍스트는 DB와 상호작용하는 1차 캐시 역할을 수행해서<br>엔티티를 한 번 조회하면, 그 객체는 영속성 컨텍스트 안에 저장되어 트랜잭션이 끝날 때까지 캐시처럼 유지
    -  객체가 1차 캐시에 있기 때문에, 트랜잭션이 커밋되거나 flush()가 일어날 때 변경된 부분을 자동으로 감지하고, DB에 반영
  2. EntityManager의 변경 감지(Dirty Checking)
    - 영속(managed)된 엔티티가 수정된 것으로 표시되어 있다면, 플러시(flush) 과정에서 UPDATE SQL이 자동 생성<br>
    이때 더티 채킹(Dirty Checking) 메커니즘은 엔티티가 처음 로드된 이후로 변경되었는지 여부를 판단
    - 개발자가 직접 UPDATE를 호출하지 않아도, 엔티티의 변경 사항을 자동으로 감지하고 DB에 반영
  3. 지연 로딩(FetchType.LAZY)
    - 지연 로딩은 DB가 아닌 프록시에서 데이터를 가져옴, 조회할 객체만 조회, 나머지 연관 객체는 모두 프록시로 처리
    - 즉시 로딩(FetchType.EAGER)
      - 즉시 로딩은 조회한 객체와 연관된 모든 객체를 한번에 조회
      - JQPL에서 N+1 문제가 발생
> N+1 문제<br>
> N+1 문제는 RDB와 객체 지향의 패러다임 간극에 의해 발생하는 문제.<br> 1개의 쿼리를 실행한 후에, 관련된 N개의 데이터를 각각 가져오기 위해 추가적으로 N번의 불필요한 쿼리가 실행되어 성능 저하가 발생
> <br>객체는 연관 관계를 통해 레퍼런스를 가지고 있으면 언제든지 메모리 내에서 Random Access를 통해 연관 객체에 접근할 수 있지만,<br> RDB의 경우 SELECT 쿼리를 통해서만 조회할 수 있기 때문에 발생
> <br>즉시 로딩을 사용하면 불필요한 테이블의 SELECT 쿼리문까지 날아가서 불필요한 조회가 발생
> <br>지연 로딩은 해당 엔티티에 접근할 때 쿼리문이 발생하므로 성능이 개선

# JPQL
- JPQL은 JPA의 일부로, 쿼리를 데이터베이스의 테이블이 아닌 JPA 엔티티, 즉 ’객체‘를 대상으로 작성하는 객체 지향 쿼리 언어
## JPQL 사용하기 : repository 인터페이스
> JPQL 사용하는 두가지 방법
> - Native SQL과 병행 사용이 가능하며, 트랜잭션 관리, 엔티티 작업 관리에 탁월한 **EntityManager 인터페이스**
> - 간결함과 일관성 유지에 탁월한 **repository 인터페이스**
### repository 인터페이스를 활용하여 JPQL 날리는 방법
Member 엔티티
```
package com.example.umc9th.domain.member.entity;


import com.example.umc9th.domain.member.enums.Gender;
import com.example.umc9th.domain.store.enums.Address;
import com.example.umc9th.global.auth.enums.SocialType;
import com.example.umc9th.global.entity.BaseEntity;
import jakarta.persistence.*;
import lombok.*;

import java.time.LocalDate;

@Entity
@Builder
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor(access = AccessLevel.PRIVATE)
@Getter
@Table(name = "member")
public class Member extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "name", length = 3, nullable = false)
    private String name;

    @Column(name = "gender", nullable = false)
    @Enumerated(EnumType.STRING)
    @Builder.Default
    private Gender gender = Gender.NONE;

    @Column(name = "birth", nullable = false)
    private LocalDate birth;

    @Column(name = "address", nullable = false)
    @Enumerated(EnumType.STRING)
    private Address address;

    @Column(name = "detail_address", nullable = false)
    private String detailAddress;

    @Column(name = "social_uid", nullable = false)
    private String socialUid;

    @Column(name = "social_type", nullable = false)
    @Enumerated(EnumType.STRING)
    private SocialType socialType;

    @Column(name = "point", nullable = false)
    private Integer point;

    @Column(name = "email", nullable = false)
    private String email;

    @Column(name = "phone_number")
    private String phoneNumber;

}
``` 
- 예시 요구사항 : ‘마크’라는 이름을 가지며, deleted_at이 NULL인 상태 (활성화 상태)인 회원을 조회
  - 메서드 이름으로 쿼리 생성
    - Spring Data JPA는 메서드 이름을 기반으로 자동으로 쿼리를 생성
    - 우리의 요구사항에 맞게 ‘이름’ 과 ‘상태’를 조건으로 내세운, `findByNameAndDeletedAtIsNull` 라는 메서드 이름을 정의하면 `name` 필드를 조건으로 하는 JPQL 쿼리를 생성합니다.
```
package com.example.umc9th.domain.member.repository;

import com.example.umc9th.domain.member.entity.Member;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.List;

public interface MemberRepository extends JpaRepository<Member,Long> {
    List<Member> findByNameAndDeletedAtIsNull(String name);
}
```
  이렇게 되면 Spring Data JPA는 내부적으로 SELECT * FROM `user` WHERE name = '마크' AND deleted_at is null; 쿼리를 자동 실행
  - @Query 어노테이션
    - JPQL을 직접 작성하는 방법으로 복잡한 조건이나 커스터마이징이  필요한 쿼리를 작성할 때 유리
   ```
package com.example.umc9th.domain.member.repository;

import com.example.umc9th.domain.member.entity.Member;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;

import java.util.List;

public interface MemberRepository extends JpaRepository<Member, Long> {

    @Query("select m from Member m where m.name = :name and m.deleted_at is null")
    List<Member> findActiveMember(@Param("name") String name);
}
```
- `@Query` 어노테이션을 활용하여 이름과 상태를 조건으로 조회하는 JPQL 쿼리를 작성
- `@Param` 어노테이션을 통해 JPQL 쿼리에서 사용되는 `:name` 파라미터를 메서드의 인자와 연결

추가적으로, `nativeQuery = true` 파라미터를 통해 JPQL의 쿼리문이 아닌 SQL문을 실행할 수 있습니다
# QueryDSL
- **타입 안전성을 보장**하는 **자바 기반의 쿼리 빌더 라이브러리**
- QueryDSL은 코드 기반의 쿼리 빌더를 제공하기 때문에, 컴파일 시점에 쿼리의 오류를 잡을 수 있으며 동적 쿼리 작성이 편리하고,<br>메서드 체이닝을 통한 복잡한 쿼리 작성에 유리
> 동적쿼리
> - 동적 쿼리란, 실행 시점에 쿼리의 일부가 변경될 수 있는 쿼리를 의미
> - 요구사항에 따라 쿼리의 조건 (`WHERE` 절에 해당하는)혹은 구조가 유동적으로 조정되어야 하는 경우가 있는데, 이때 동적 쿼리의 사용이 중요해집니다.
- 예시로 가게명, 지역명, 상세 주소로 검색하는 기능을 만든다고 가정
  - 각각 대응하는 JQPL쿼리문을 작성해야 함
    - 이때를 위해 동적 쿼리를 이용해 where절의 조건들을 모듈화, 조회 쿼리를 만들때 한번에 사용<br>이런 이점들로 인해 QueryDSL을 사용
# 핵심 키워드
- 지연 로딩과 즉시 로딩의 차이 : 즉시 로딩은 엔티티 조회를 할 때 모든 연관 테이블을 로드하고<br>
지연 로딩은 실제 객체를 사용할 때 로드해서 불필요한 쿼리를 방지한다.
- JPQL : 쿼리를 데이터베이스의 테이블이 아닌 JPA 엔티티, 즉 ’객체‘를 대상으로 작성하는 객체 지향 쿼리 언어
- Fetch Join : 지연 로딩으로 인한 N+1 문제를 해결하기 위한 방법으로 연관된 엔티티를 한번의 쿼리로 함께 조회
  - Ex) SELECT m FROM Member m JOIN FETCH m.team
  - 장점 : 한 번의 쿼리로 모든 연관 데이터 로딩
  - 단점 : 페이징 지원이 어려
- @EntityGraph : JPQL 없이Fetch Join 효과를 주는 방법
  - @EntityGraph(attributePaths = {"team"}) 으로 쓰면 내부적으로 JOIN FETCH m.team 형태로 동작
  - 코드 가독성이 높고 JPQL 수정 없이 지연 로딩 관계를 한번에 해결 가
- commit과 flush 차이점
  - commit은 트랜잭션이 끝날 때 flush를 내부적으로 호출하고 트랜잭션이 종료
  - flush는 변경 내용이 SQL로 DB에 반영되고 트랜잭션은 종료되지 않는다.  
- QueryDSL, OpenFeign의 QueryDSL
  - QueryDSL은 하이버네이트 쿼리 언어(HQL: Hibernate Query Language)의 쿼리를 타입에 안전하게 생성 및 관리해주는 프레임워크
    <br>QueryDSL은 정적 타입을 이용하여 SQL과 같은 쿼리를 생성할 수 있게 해준다.
  - OpenFeign의 QueryDSL은 기존 QueryDSL의 개발이 지연되면서 발생한 문제를 해결하기 위해 OpenFeign 팀이 기존 QueryDSL 프로젝트를 포크하여 유지보수한 프로젝트
    <br>Spring Boot 3의 Jakarta EE 지원 및 KSP(Kotlin Symbol Processing) 지원 등 최신 기술과의 호환성을 개선하고, CVE-2024-49203 같은 보안 취약점을 해결하여 개발자들이 보다 안전하고 현대적인 환경에서 QueryDSL을 사용할 수 있도록 지원
- N+1 문제 해결할수 있는 여러 방안들
  1. Fetch Join
  2. @EntityGraph
  3. BatchSize 설정
     - 한 번에 여러 개의 연관 엔티티를 IN 쿼리로 조회 (@BatchSize(size = 100))  
  4. DTO Projection
     - 필요한 데이터만 선택한 DTO 클래스를 만들어서 사용
- 영속 상태의 종류 
  - 비영속 (new/transient) : 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태
  - 영속 (managed) : 영속성 컨텍스트에 관리되는 상태
  - 준영속 (detached) : 영속성 컨텍스트에 저장되었다가 분리된 상태
  - 삭제 (removed) : 삭제된 상태

# 미션











      
