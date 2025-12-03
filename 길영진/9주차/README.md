# 🌱 API & Paging (1)
---

---

---
# API 시그니처 만들기
- API 시그니처는 내용에 적혀있는대로 매우 함축적인 말이다. API의 기능과 사용 방법을 정의하는 일련의 과정을 의미합니다.
- API 호출할 떄 핑요한 모든 정보와 그 결과에 따라 무엇을 얻을 수 있는지에대한 규칙을 설정하는 것입니다.
- 여기서 설명하는 것은 응답 요청을 보내면 그에 맞는 어떤 DTO를 보낼지 정의하는것
- 이 API를 기반으로 API 명세서를 작성한다.
- 
- #### 위 내용(Notion)에서 갖는 알수 있었던 점
    - DTO 리스트를 보낼때 그 리스트를 가지는 DTO를 따로 빼낸다.
    - 성공 코드와 예외 코드 만들기(작성하여 성공여부와 예외 여부를 확인)
---
# API 명세서에 기재하기
-  API 명세서를 작성하는 이유
    1. 프론트앤드 개발자의 병목을 최소호 람
   2. 프프론트앤드가 API를 물어보지 않고 알 수 있기 위해
   3. 여기서 Swagger를 통해서 API 명세서를 대신하기도 함
   ```java
    // 가게의 리뷰 목록 조회
    @Operation(
            summary = "가게의 리뷰 목록 조회 API By 마크 (개발 중)",
            description = "특정 가게의 리뷰를 모두 조회합니다. 페이지네이션으로 제공합니다."
    )
    @ApiResponses({
            @io.swagger.v3.oas.annotations.responses.ApiResponse(responseCode = "200", description = "성공"),
            @io.swagger.v3.oas.annotations.responses.ApiResponse(responseCode = "400", description = "실패")
    })
    @GetMapping("/reviews")
    public ApiResponse<ReviewResDTO.ReviewPreViewListDTO> getReviews(){

        ReviewSuccessCode code = ReviewSuccessCode.FOUND;
        return ApiResponse.onSuccess(code, null);
    }
    ```
   위 코드에 대한 설명
  - @Operation: API 설명을 작성, summary에 API 제목 description에 API 설명을 작성
  - @ApiResponse: 코드와 응답 메시지를 작성
- @ApiResponses: @ApiResponse를 묶음
  - 여기서 이부분을 EKfh Docs 전용 파일로 만들어서 작성가능
---
# 서비스 매서드 로직 작성
- 비즈니스 로직을 작성하는 구간
- 서비스에서 응답 DTO를 만들어도 되고, 컨트롤러에서 만들어도 됨
- JPA에서 제공하는 페이징을 사용하기 위해서는 몇가지가 필요
  1. PageRequest를 생성: 페이징할 정보 (Offset, Limit을 정의)
  2. Repository에 PageRequest 삽입
---
# 핵심 키워드
## 1. Spring Data JPA의 Page와 Slice
- 스프링에서는 페이지를 나누기 위해서 Pagination을 위한 두 가지 객체를 제공한다.
- Page는 Slice를 상속한다. (Slice가 가진 메서드를 Page도 사용가능)
- 여기서 Page와 Slice가 다른점은 Page는 조회 쿼리 이후 젠체 데이터 개수를 조회하는 퀴리가 한번 더 실행된다는 것
- Slice는 조회 쿼리만 날림
- Slice와 Page 사용 점
  - Slice는 무한 스크롤 등을 구현하는 경우 유용
  - Page는 전체 페이지 개수나 데이터 개수가 필요한 경우 유용
  - 데이터 양이 많아진다면 Slice를 사용하는 것이 성능상은 유리

## 2. 객체 그래프 탐색
- 객체 모델과 데이터 모델 간의 구조적 차이 때문에 벌어지는 문제
  1. 객체 모델은 상속을 자연스럽게 지원하지만, 관계형 데이터베이스에서는 이를 직접적으로 지원하지 않는다.
  2. 객체 모델에서는 객체 간의 연관관계를 직접 참조로 표현할 수 있지만, 관계형 데이터베이스에서는 외래 키(Foreign Key)를 사용해 연관관계를 표현해야 한다.
  3. 객체는 참조 동등성을 사용하지만, 데이터베이스에서는 기본 키를 사용하여 행의 동등성을 판단한다.
  4. 객체 모델에서는 필요한 데이터를 즉시 로딩할 수 있지만, 데이터베이스에서는 조인을 통해 데이터를 가져와야 한다.
- 객체지향 프로그램은 위와 같은 이유로 객체 그래프 탐색을 할 수 없는데 JAP는 이를 해결하여 준다.
- JPA에서 단방향 연관관계 매핑은 엔티티 간의 관계를 설정하는 방식으로 한쪽이 주인이 되어 탐색
- 양방향은 단방향 연관관계 2개를 만들어 해결
# 학습 후기
- API 시그니처의 정의 및 응답 DTO 설계 방식을 이해했고, Swagger 어노테이션을 활용한 API 명세서 작성법을 학습했습니다.
- Spring Data JPA의 Page와 Slice의 차이점(총 개수 조회 유무)과 각각의 사용 용도를 명확히 파악했으며, 객체 그래프 탐색의 개념을 이해했습니다.
# 미션
### 1주차 사진
 <img alt="erd0" src="https://github.com/mybookG/image/blob/main/erd0.png?raw=true" />

### github 링크
https://github.com/mybookG/Umc_personal_repository