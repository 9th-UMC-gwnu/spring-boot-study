# 🌱QueryDSL 추가 학습(1)

# QueryDSL? 동적 쿼리?
- 정적 타입을 이용해 SQL과 같은 쿼리를 생성할 수 있도록 지원하는 프레임워크
- 문자열이나 XML 파일을 통해 쿼리를 작성하는 대신 플루언트(Fluent) API를 활용해 쿼리를 생성

## Fluent API, DSL, QueryDSL
- 여러 메소드를 체인(chain)처럼 연결하여 영어 문장처럼 읽히도록 만든 객체 지향 API 디자인 패턴
- 단순 문자열과 비교해서 Fluent API를 사용할 때의 장점은 다음과 같다.
1. IDE의 코드 자동 완성 기능 사용
2. 문법적으로 잘못된 쿼리를 허용하지 않음
3. 도메인 타입과 프로퍼티를 안전하게 참조할 수 있음
4. 도메인 타입의 리팩토링을 더 잘 할 수 있음
---
## 동적 쿼리란?
- 런타임에 입력에 따라 WHERE / JOIN / ORDER BY 등의 조건을 동적으로 구성해 SQL을 생성하는 기법
- 실행 시점에 쿼리 문자열을 만들어 DB에 전달하고 실행하는 것
## Q클래스 
```text
쿼리 전용 클래스를 만드는 과정: 객채 + 쿼리 메타데이터
```
- 엔티티를 기반으로 생성되는 쿼리 전용 클래스
- 데이터베이스 쿼리를 Fluent 스타일의 내부 DSL로 안전하게 작성할 수 있도록 지원하는 “타입 안전 쿼리 객체
```
Q클래스 장점
  1. 타입 안정성 : 컴파일 단계에서 엔티티 기반으로 쿼리를 생성하므로, 잘못된 필드나 타입 사용 시 에러 발생 → 런타임 오류 예방
  2. 자동완성 : 속성 이름을 외울 필요 없이 IDE 자동완성 기능을 활용해 쿼리를 작성 가능 → 개발 효율 상승
  3. Fluent Interface 스타일 : 메서드 체인을 활용해 자연스럽고 읽기 쉬운 쿼리 작성 가능 → 코드 가독성 향상
  4. 내부 DSL(Internal DSL) 효과 : SQL/JPQL 문법을 자바 코드 안에서 도메인 특화 언어처럼 표현 가능 → 의도 직관적
  ```
- 여기서 Q클래스는 객체 구조가 변경될 때 마다, 자바 버전만다 다르게 생성되기 때문에 다른이와 공유하면 안된다.
--- 
# 핵심 키워드
### 1. QueryDSL에서 FetchJoin 하는 법
- Fetch jojn은 한 번의 쿼리로 JOIN 대상 테이블 데이터까지 한 번에 가져오고 영속성 컨텍스에 넣어주기 때문에 n+1 문제를 해결하기 위한 하나의 방법
- leftJoin()이나 innerJoin()이후 fetchJoin()을 불러온다
-  @OneToMany를 참조하려고 할때는 중복이 발생할 수 있기 때문에 distinct()를 추가한다.
```text
결과 반환
fetch : 조회 대상이 여러건일 경우. 컬렉션 반환
fetchOne : 조회 대상이 1건일 경우(1건 이상일 경우 에러). generic에 지정한 타입으로 반환
fetchFirst : 조회 대상이 1건이든 1건 이상이든 무조건 1건만 반환. 내부에 보면 return limit(1).fetchOne() 으로 되어있음
fetchCount : 개수 조회. long 타입 반환
fetchResults : 조회한 리스트 + 전체 개수를 포함한 QueryResults 반환. count 쿼리가 추가로 실행된다.
```
### 2. DTO 매핑 방식 (+DTO안에 DTO)
- #### 수동 매핑
  - 가장 기본적이며 직관적이며 매핑 과정 정게 가능
  - 필드가 많거나 엔티티가 복잡하면 반복 코드가 많아진다.
  - 유지 보수 어렵다
- #### 생성자/빌더를 이용한 매핑
  - 불변 객체를 만들기 좋고 가독성이 매우 좋다.
  - 빌더를 사용하여 만들면 선택적 필드 처리도 깔끔하다.
  - 생성자나 빌더가 많아지면 복잡하다.
- #### 매핑 라이브러리 사용
  - MapStruct : 컴파일 시점에 자동으로 매핑 코드 생성되고 타입 변환 및 중첩 DTO 지원
  - ModelMapper : 런타임에 동적으로 매핑돠고 설정에 따라 필드 자동 매핑된다.
  - 반복 코드를 최소화 하지만 자동 매핑 실패 시 디버깅에 어려움을 겪을 수 있음.
- #### DTO안에 DTO
  - 연관 엔티티를 DTO로 감싸서 계층 구조 표현
  - MapStruct와 같은 라이브러리도 쉽게 매핑 가능
  ```java
    class AddressDTO {
    private String city;
    private String street;
    }

    class UserDTO {
    private String name;
    private AddressDTO address;
    }
    ```
### 3. 커스텀 페이지네이션
- 페이지네이션은 데이터를 한 번에 다 가져오지 않고, 일정 단위로 나눠서 조회한다.
- 스프링에서는 다음과 같이 사용된다.
    ```java
    Pageable pageable = PageRequest.of(1, 10, Sort.by("name").ascending());
    Page<User> page = userRepository.findAll(pageable);
    ```
- 정렬 기준이 여러개이거나 엔티티 필드가 아닌 값으로 정렬/필터링을 할 떄 사용한다.
- 일반적으로 페이지 번호(page)와 페이지 크기(size)를 사용

### 4. transform - groupBy
- transform()는 QueryDSL이 쿼리 결과를 받아 특정 객체 형태로 변환해주는 메서드이다.
- groupBy()는 transform()내에서 사용되며, 특정 기준으로 결과를 그룹화하는 역할을 합니다.
- 예시를 보자
  - 부모 Entity인 Faq가 자식 Entity인 FaqImage를 List로 가지고 있을 때, Faq를 조회할 때 FaqImage를 프론트에 어떻게 보내줄까?
   ```spring-mongodb-json
   1. faqRespository.findByFaqId(faqId)로 faq 객체를 한번 찾고,
   2. faqImageRepository.findAllByFaqId(faqId) 를 찾아서
   3. Response 객체에 담아주는 방법도 있을것이다.
   이 과정은 select구문이 두번 날아가게 된다. 또한 자식 객체를 자동으로 List로 변환이 안된다.
  -> 이것을 대체하기 위해 .transform(groupBy())를 사용
     부모객체 하나에 자식객체를 리스트로 붙여서 반환하여 줄 수 있다.
    ```
### 5. order by null
- ORDER BY NULL 은 말 그대로 NULL값을 기준으로 정렬하는 것이다
- ORDER BY는 특정 기준으로 정렬하는 것
- NULL은 값이 존재하지 않는곳 0이랑은 다른개념이다.
- 여기서 MYSQL기준으로 ORDER BY NULL을 하면 NULL값이 맨 위로 뜨는 정렬이 수행된다.
- 이것을 바꾸려면 NULL값 뒤에 또 다른 기준항목을 포함시키면 된다.
---

# 학습 후기
- QueryDSL을 활용하면 타입 안전성과 자동완성을 통해 동적 쿼리를 훨씬 직관적으로 작성할 수 있다는 것을 알게 되었다.
- 특히 transform과 groupBy를 이용한 DTO 매핑과 커스텀 페이지네이션 방식이 실무에서 유용하게 쓰일 것 같다.
# 미션
### 1주차 사진
 <img alt="erd0" src="https://github.com/mybookG/image/blob/main/erd0.png?raw=true" />

### github 링크
https://github.com/mybookG/Umc_personal_repository/pull/1