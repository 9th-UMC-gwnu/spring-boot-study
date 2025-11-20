# Chapter 9. API & Paging
## 페이징이 필요한 이유
- 리뷰가 만약 200개가 존재한다 가정했을 때, 한 번에 200개를 다 보여주면 엄청난 렉이 발생하고 따라서 이를 끊어서 보여줘야 하고, 이렇게 끊어서 보여주는 것을 페이징이라 함
> API 제작 순서 (API URL는 설계되었다는 전제)
> 1. API 시그니처를 만든다
> 2. API 시그니처를 바탕으로 API 명세서에 명세를 해준다
> 3. 비즈니스 로직을 만든다
> 4. 컨트롤러를 완성한다
> 5. validation 처리를 한다

## API 시그니처 만들기
1. 어떤 요청을 보내면 그에 맞는 응답을 보낼 DTO를 정의한다
   - Query Parameter, Request Body가 필요한 경우 함께 정의한다
2. 해당 API를 기반으로 API 명세서에 기재한다
- 서버가 프론트에게 요청을 받을 시그니처를 http 메서드, uri, dto를 통해 명시해 주어야 함
  
## API 명세서에 기재하기
- 아직 안 만들었는데 API 명세서에 명세하는 이유
  - 프론트엔드 개발자의 병목을 최소로 하기 위함
  - API 명세서에 기재하지 않으면 프론트엔드 개발자는 어떤 API가 있는지 어떤 응답을 보내는지 알기 위해서 백엔드 중 담당 팀원에게 직접 물어봐야 알 수 있음
  - DTO가 바뀔때마다 계속 업데이트 해야해서 Swagger로 대체하는 경우도 있음
- Swagger 용 어노테이션
  - @Operation: API 설명을 작성, summary에 API 제목 description에 API 설명을 작성
  - @ApiResponse: 코드와 응답 메시지를 작성
  - @ApiResponses: @ApiResponse를 묶음
- ControllerDocs로 따로 인터페이스 선언후 Swagger 어노테이션으로 명세

## 서비스 메서드 로직 작성
- API의 핵심, 비즈니스 로직을 작성
- 순서
  1. 서비스 인터페이스 정의 & 컨트롤러 연결
  2. 서비스 작성
    - 페이징 순서<br>1. PageRequest를 생성: 페이징할 정보 (Offset, Limit을 정의)<br>2. Repository에 PageRequest 삽입
  3. 컨버터 제작 & 서비스 연결
  4. 서비스 완성

## 핵심키워드
- 객체 그래프 탐색
  - 엔티티가 연관된 객체를 점(.)으로 계속 따라가며 탐색하는 것 
  - 예시 : user.getReviews().get(0).getStore().getName();
## 미션
[9주차 미션](https://github.com/hyukkimm/UMC/tree/Feat/Chapter9)

## 후기
- 페이징이 쓰이는 경우와 하는법을 배워서 실제 코드를 짤 때 잘 쓸 수 있을 것 같다.
