## Chapter 9. API & Paging
## 핵심 키워드
### 객체 그래프 탐색(Object Graph Navigation)
- 엔티티 객체의 연관된 객체를 점(.) 연산자로 자연스럽게 탐색하는 방식(order.getMember().getAddress())
- 속성: 지연로딩(Lazy)과 즉시로딩(Eager)에 영향을 받으며, 엔티티 관계를 깊게 탐색할수록 N+1 문제가 발생할 수 있음.
- 장점: 코드가 단순하고 직관적이며, 객체지향 방식으로 연관 데이터를 조회할 수 있음.
- 단점: 엔티티 그래프 깊이만큼 의도치 않은 추가 쿼리(N+1)가 발생하여 성능 저하가 쉽게 생김.

## 학습후기
- 컨트롤러, 컨버터에 좀 더 익숙해진 기분이었다.

## 미션
https://github.com/sungw00ng/UMC-week-mission/tree/FEAT/Chapter9

## 미션 기록
<img width="1000" height="600" alt="스크린샷 2025-12-03 오후 2 54 37" src="https://github.com/user-attachments/assets/8820c5b8-e6b6-46c2-93ef-394cc4cb10ff" />

## 1. 내가 작성한 리뷰 목록 조회하기
- /users/{userId}/reviews	GET

## 2. 특정 가게의 미션 목록 조회하기
- /stores/{storeId}/missions GET

## 3. 내가 진행 중인 미션 목록 조회하기
- /users/{userId}/missions/accept POST
- /users/{userId}/missions GET

## 4. 진행 중 미션 완료 및 조회 테스트
- /users/{userId}/missions/{userMissionId}/complete PATCH
- /users/{userId}/missions/{userMissionId} GET
- /users/{userId} GET
