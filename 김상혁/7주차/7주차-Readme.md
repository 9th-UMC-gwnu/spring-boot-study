# API 응답 통일
- 응답 코드의 경우 enum으로 관리
- 하나의 enum으로 성공, 실패 응답을 관리 할수도 있고, 분리할 수도 있음
## 통일이 필요한 이유
- 보통 API 자체도 함수처럼 호출해서 하나의 변수에 그 응답을 받는데
그 응답의 형태가 제각각이면 혼란스러움
```
// 일반적인 형태
{
	"isSuccess" : Boolean 타입
	"code" : String // HTTP 상태 코드 외에 더 세부적인 결과를 알려주기 위해 사용
	"message" : String // code에 추가적으로 어떤 결과인지 알려주기 위해 사용
	"result" : {응답으로 필요한 또 다른 json} //  응답에 필요한 json (DTO와 같은 것)
}
```
---
# 예외 처리
<img width="453" height="162" alt="스크린샷 2025-10-12 17 24 48" src="https://github.com/user-attachments/assets/88dd8517-8453-469e-bef5-36241980ca78" />

- 비즈니스 로직을 작성하면서 사진과 같은 예외 상황을 마주칠 때가 많아<br>
그때마다 Exception으로 처리해버리면 어떤 도메인의 예외인지 확인하기 어려운 단점이 존재
- 각 도메인별 Exception을 정의, 프로젝트 Exception 상속을 통해 해결
  - 글로벌에 프로젝트 Exception, 도메인에 도메인 Exception을 넣는 방식
## 에러 핸들러
- 모든 도메인 Exception을 모아서 응답을 통일하고 브라우저에 전달하는 객체
  - 프로젝트 Expception을 도메인 Exception이 상속하므로 프로젝트 Exception만 잡으면 됨

# 핵심 키워드
- RestControllerAdvice
  - 모든 Controller에서 발생한 예외를 한 곳에서 잡아서 공통 형태로 처리
- lombok
  - 반복되는 코드를 자동으로 생성해주는 라이브러리로 Getter,Setter,생성자 등을 포함
- dto 형식 public static VS record 비교하기
  - public static 클래스는 @ builder를 통해 유연한 생성이 가능하고 내부 구조 계층화가 가능
  - record 방식은 코드가 짧고 불변성을 자동 보장해줘서 DTO 용도로 깔끔하고 직관적
  - public static은 빌더나 중첩 구조가 필요한 복합 DTO에 record는 단순히 값만 전달하는 DTO에 적합
# 미션
- RestControllerAdvice 장점
  1. 전역 예외 처리 : 모든 컨트롤러에서 발생한 예외를 한곳에서 처리
  2. 응답 형식 통일 : 모든 오류 응답을 공통 구조(ApiResponse)로 감쌀 수 있다.
  3. 유지보수 용이 : 에러 코드를 수정할 때 한 곳만 수정하면 된다.
- 없으면 불편한 점
  1. 컨트롤러마다 try-catch를 통해 예외를 잡아내야 한다.
  2. 응답 구조 불일치 : 컨트롤러마다 에러 응답이 달라서 프론트에서 처리하기 힘들다.
  3. 유지보수를 하려면 모든 컨트롤러를수정해야 한다.
  4. 로그와 응답 형식이 통일되지 않아 원인 파악이 힘들다.

- github : https://github.com/hyukkimm/UMC/tree/Feat/Chpater7
# 학습후기
- 프론트와 협업할 때 api를 잘 통일해야 겠다고 느꼈다. 그리고 계층형으로 만드는 것보다 도메인형으로 만드는게 더 편한거 같다.
