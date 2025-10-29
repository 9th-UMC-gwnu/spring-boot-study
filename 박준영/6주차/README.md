# QueryDSL

- 정적 타입을 이용해 SQL과 같은 쿼리를 생성할 수 있도록 지원하는 프레임워크
- 문자열이나 XML 파일을 통해 쿼리를 작성하는 대신 플루언트(Fluent) API를 활용해 쿼리를 생성


### 플루언트(Fluent)

- 메서드 체이닝을 활용해 코드의 흐름을 자연스럽게 읽히도록 설계된 API 스타일

**장점**
1. IDE의 코드 자동 완성 기능을 활용할 수 있음
2. 문법적으로 잘못된 쿼리를 컴파일 시점에서 방지함
3. 도메인 타입과 프로퍼티를 안전하게 참조할 수 있음
4. 도메인 구조 변경 시(리팩토링 시) 컴파일 에러로 즉시 감지 가능 — 유지보수가 쉬움


## 동적 쿼리

- 런타임에 입력(사용자 필터, API 파라미터 등)에 따라 WHERE / JOIN / ORDER BY 등의 조건을 동적으로 구성해 SQL을 생성, 실행하는 기법


## Q 클래스

- 엔티티(Entity)를 기반으로 생성되는 쿼리 전용 클래스
- 데이터베이스 쿼리를 Fluent 스타일의 내부 DSL로 안전하게 작성할 수 있도록 지원하는 **타입 안전 쿼리 객체**

**장점**
1. **타입 안정성**: 컴파일 단계에서 엔티티 기반으로 쿼리를 생성하므로, 잘못된 필드나 타입 사용 시 에러 발생 → 런타임 오류 예방
2. **자동완성**: 속성 이름을 외울 필요 없이 IDE 자동완성 기능을 활용해 쿼리를 작성 가능 → 개발 효율 상승
3. **Fluent Interface 스타일**: 메서드 체인을 활용해 **자연스럽고 읽기 쉬운 쿼리** 작성 가능 → 코드 가독성 향상
4. **내부 DSL(Internal DSL) 효과**: SQL/JPQL 문법을 자바 코드 안에서 **도메인 특화 언어처럼 표현** 가능 → 의도 직관적


## JPARepository 작성법

- BooleanBuilder를 활용 → 다양한 조건절 이용

**예시**

// 서비스 계층
public class ReviewQueryService {

    List<Review> searchReview() {
        // Q클래스 정의
        QReview review = QReview.review;
        // BooleanBuilder 정의
        BooleanBuilder builder = new BooleanBuilder();

        // 동적 쿼리 조건 구성
        if (type.equals("location")) {
            builder.and(review.store.location.name.contains(query));
        }
        if (type.equals("star")) {
            builder.and(review.star.goe(Float.parseFloat(query)));
        }
        if (type.equals("both")) {
            String firstQuery = query.split("&")[0];
            String secondQuery = query.split("&")[1];
            builder.and(review.store.location.name.contains(firstQuery));
            builder.and(review.star.goe(Float.parseFloat(secondQuery)));
        }

        // Repository 호출
        List<Review> reviewList = reviewRepository.searchReview(builder);

        return reviewList;
    }
}


// 레포지토리 계층 searchReview() 메서드
@Override
public List<Review> searchReview(Predicate predicate) {

    // JPAQueryFactory 세팅
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);
    
    QReview review = QReview.review;

    return queryFactory
        .selectFrom(review)
        .where(predicate)
        .fetch();


    /*  Join 문

        return queryFactory
            .selectFrom(review)
            .leftJoin(store).on(store.id.eq(review.store.id))
            .leftJoin (location).on(location.id.eq(store.location.id))
            .where(predicate)
            .fetch();

    */
}


# 핵심 키워드

- **QueryDSL에서 FetchJoin 하는 법**

    **예시**

  queryFactory.selectFrom(review)

  // **leftJoin()** : 조인 대상 지정, **fetchJoin()** : 연관된 엔티티 즉시 로딩
  .leftJoin(review.store, store).fetchJoin()  // Review → Store 페치 조인
  .leftJoin(store.location, location).fetchJoin() // Store → Location 페치 조인
  .where(predicate)
  .fetch();                                        // 실제 DB 조회


- **DTO 매핑 방식 (+DTO안에 DTO)**

    **예시**
    
    List<ReviewResponseDTO> results = queryFactory
    
    // **ReviewResponseDTO 생성자를 만들어 조회하여 나온 컬럼을 넣어줌**
    .select(Projections.constructor(ReviewResponseDTO.class,   
    review.content,
    review.star,
    
    // **StoreResponseDTO 생성자를 만들어 조회하여 나온 컬럼을 넣어줌**
    Projections.constructor(StoreResponseDTO.class, store.name,
    store.location)))
    .from(review)
    .leftJoin(review.store, store)
    .fetch();
    
    **Projections.constructor()은 생성자를 이용해 SQL 쿼리문의 결과를 자바 객체로 만들어주는 기능**


- **커스텀 페이지네이션**

  - QueryDSL로 limit/offset을 지정해 페이지를 구현하는 것

  **예시**

  List<ReviewResponseDTO> results = queryFactory
                            .select(Projections.constructor(ReviewResponseDTO.class 
                                        ,review.content
                                        ,review.star
                                        ,Projections.constructor(StoreResponseDTO.class, store.name,
                                                store.location)))
                            .from(review)
                            .leftJoin(review.store, store)
                            .offset(Pageable.getOffset())   // 시작 위치
                            .limit(Pageable.getPageSize())  // 한 페이지당 크기
                            .fetch();


- **transform - groupBy**

  **groupBy(Entity.id)**

    - 엔티티의 id를 기준으로 그룹화

  **transform()**

    - 그룹화된 결과를 Map<키, 값> 형태로 반환


- **order by null**

  - 정렬을 생략하여 저장된 순서대로 반환
    → 즉, 정렬연산을 하지 않아 성능 개선

  **자주 쓰이는 경우**

    - Group By 뒤
        - MySQL에서 Gruop By는 자동으로 정렬을 수행하는데 정렬이 불필요한 경우 order by null을 사용해 성능을 개선
    - union 뒤
        - union도 중복 제거를 위해 정렬을 수행하기 때문에 중복이 없거나 정렬이 필요없는 경우 order by null을 사용해 성능을 개선


# 학습 후기

- 동적 쿼리를 효율적으로 해결할 수 있는 QueryDSL의 Predicate 인터페이스, JpaQueryFactory 클래스를 학습할 수 있어 유익했다.

# 미션

- 깃허브 주소 (https://github.com/nemm3623/UMC-chapter4-mission/tree/Feat/Chapter6)