# Chapter 8. API & Swagger & Annotation (1)
```TEXT
I. 계층 및 검증 흐름
II. API 명세화(Swagger)
III. 데이터 검증 로직 및 Repository 연동
IV. 예외 처리 통합
V. DTO 구조 개선
```
## I. 계층 및 검증 흐름
### UserController
- @Valid를 통해 요청 DTO에 대한 유효성 검증을 트리거
- 검증 통과 시 Service 호출 후 ApiResponse 형태로 응답
### UserRequest.SignUpDTO
- foodPreference 필드에 `@ExistFoods` 어노테이션 적용
- 존재하지 않는 음식 ID 입력 시 검증 실패 발생

## II. API 명세화(Swagger)
- `@Operation`, `@ApiResponses`, `@Tag` 추가
- Swagger UI를 통해 회원가입 API의 요청·응답 스펙 자동화
- 코드-문서 일체화로 유지보수성 향상

## III. 데이터 검증 로직 및 Repository 연동
### FoodRepository
- `existsById(Long id)` 메서드 추가
- Validator가 DB를 조회해 음식 존재 여부 확인 가능

### ExistFoodsValidator
- foodRepository를 주입받아 유효성 검증 수행
- 검증 실패 시 지정된 메시지와 함께 예외 발생

## IV. 예외 처리 통합
### GeneralExceptionAdvice
- MethodArgumentNotValidException 등 유효성 검증 실패 시 예외 처리
- 모든 실패 응답을 ApiResponse 포맷으로 통일
- 상태 코드 400 반환 및 필드별 에러 메시지 매핑

## V. DTO 구조 개선
### UserConverter / UserResponse
- `createdAt` 필드를 응답 DTO에 추가
- 변환 로직을 정리해 통일된 ApiResponse 스펙 준수

## 후기
- Validation과 Swagger를 함께 다루면서
API 흐름이 좀 더 명확해지고 깔끔해진 느낌이었다.

## 핵심 키워드
### java의 Exception 종류들
Checked Exception == IOException,SQLException,FileNotFoundException  <br>
Unchecked Exception == NullPointerException, ArrayIndexOutOfBoundsException,
IllegalArgumentException,ArithmeticException 

### @Valid
- 객체 검증 어노테이션

## Swagger 유효성 검증 테스트
<img width="1000" height="500" src="https://github.com/user-attachments/assets/5e2a477b-1351-420f-b4ed-801d39cc42cf" /><br>


## 미션
- https://github.com/sungw00ng/UMC-week-mission/tree/FEAT/Chapter8

## 미션 기록
| 번호    | 기능                  | HTTP Method / URI                | 주요 변경 사항                                                                                                                                                            |
| ----- | ------------------- | -------------------------------- | -----------------|
| 1 | 가게 추가 | `POST /locations/{id}/stores` | StoreController, StoreService, StoreRepository<br>LocationRepository 사용<br> StoreRequest/Response DTO 추가 |
| 2 | 리뷰 추가 | `POST /stores/{id}/reviews` | ReviewController, ReviewService, ReviewRepository<br>StoreRepository 사용<br>ReviewRequest/Response DTO 추가 |
| 3 | 미션 추가 | `POST /stores/{id}/missions` | MissionController <br> MissionService, MissionRepository(수정)<br>StoreRepository 사용 <br>MissionRequest/Response DTO 추가 |
| 4 | 미션 도전(수락) | `POST /users/{id}/missions/{id}` | UserController, UserService, UserMissionRepository <br>UserResponse DTO에 “미션 수락 결과” 필드 추가   |

