# API(Application Programming Interface)란?

- 어려운 것은 감추고 보다 쉽게 상호작용 할 수 있도록 해주는 것들을 의미한다.
- API는 애플리케이션을 프로그래밍 할 때, 보다 쉽게 할 수 있도록 해주는 도구들을 의미한다.

# REST API

- REST는 Representational State Transfer의 약자로, HTTP 메소드와 자원을 이용해 서로 간의 통신을 주고받는 방법이다.

# HTTP Method

- GET   : 조회를 할 때 사용. 쿼리 스트링은 무방비로 노출되므로, 조심해야 한다.
- POST  : 데이터를 처리하고, 바디를 통해 데이터를 전달한다. 주로 신규 리소스를 생성하고, 등록하고, 업데이트하는데 사용한다.
- PUT   : 리소스를 완전히 대체한다.
- PATCH : 리소스를 부분 변경한다.
- DELETE: 리소스를 제거할 때 사용한다.

> 멱등성이란?
> - 멱등성(Idempotency)는 동일한 연산을 여러번 수행해도 결과가 달라지지 않는 성질을 의미한다
> - GET, PUT, DELETE 메소드는 멱등성을 가지며, PATCH, POST 메소드는 멱등성을 가지지 않는다.

# RESTful API Endpoint의 설계

## 1. URI (Uniform Resource Identifier)

- 하나의 자원을 고유하게 식별하는 문자열
- URL과 URN을 포함하는 상위 개념이다.

## 2. URL (Uniform Resource Locator)

- 자원의 위치를 식별하는 문자열
- 특정 자원의 위치와 접근 방법을 지정한다.

> URL 구조
> - Scheme  : 자원에 접근하기 위한 통신 프로토콜
> - Host    : 서버의 도메인 이름 또는 IP 주소
> - Port    : 자원에 접근하기 위한 포트 번호
> - Path    : 서버 내 자원이 위치하는 경로
> - Query   : 자원에 대한 추가 질의를 위한 key = value 형식의 데이터
> - Fragment: 자원의 특정 부분을 가리키는 값. 서버에는 전송되지 않는다.

## 3. URN (Uniform Resource Name)

- 자원의 고유한 이름을 식별하는 문자열
- 자원의 위치가 변경되더라도 고유하게 식별할 수 있도록 설계되었다.
- 활용이 까다롭기 때문에 실제 사용 사례는 제한적이다.

> URN 구조
> - Scheme                        : 항상 "urn"으로 고정된다.
> - NID(Namespace Identifier)     : 자원이 속한 네임스페이스를 의미한다.
> - NSS(Namespace Specific String): 네임스페이스 내에서 자원을 고유하게 식별하는 문자열

## RESTful한 API의 설계를 위한 규칙

1. 동사가 포함이 되어선 안된다.
2. URI에서 단어의 구분이 필요한 경우 -을 이용한다.
3. 자원은 기본적으로 복수형으로 표현한다.
4. 단 하나의 자원을 명시적으로 표현하기 위해서는 /users/id 와 같이 식별 값을 추가로 사용한다.
5. 자원 간 연관 관계가 있을 경우 이를 URI에 표현한다.

# 세부적인 API 설계

## Path variable

> - GET /users/articles/{articleId}
> - GET /users/articles/{article-id}

## Query String

> - GET /users/articles?name=umc
> - GET /users/articles?name=umc&owner=ddol

## Request Body

- json의 형태 혹은 form-data 형태로 전송.

```json
{
	"name" : "김주헌",
	"phoneNum" : "010-1111-2222",
	"nickName" : "mark"
}
```

## Request Header

- 전송에 관련된 기타 정보들이 담기는 부분

# 학습 후기

- REST API에 대한 전반적인 개념과 설계 방법에 대해 알게 되었다.
- URI, URL, URN의 차이점에 대해 명확히 알게 되었다.
- Path variable, Query String, Request Body, Request Header의 사용법에 대해 이해하게 되었다.

# 미션

## 홈 화면

- GET /api/v1/home
- Authorization: Bearer {accessToken}

Response Body
```json
{
  "success": true,
  "message": "홈 화면 조회 성공",
  "data": {
    "locations": "안암동",
    "myPoint": 999999,
    "successMissionCount": 7,
    "myMissions": [
      {
        "missionId": 1,
        "storeName": "반이학생마라탕",
        "storeCategory": "중식당",
        "missionTitle": "10000원 이상의 식사",
        "missionDate": "2025-09-20",
        "missionStatus": "COMPLETED",
        "point": 500
      },
      {
        "missionId": 2,
        "storeName": "반이학생마라탕",
        "storeCategory": "중식당",
        "missionTitle": "10000원 이상의 식사",
        "missionDate": "2025-09-20",
        "missionStatus": "COMPLETED",
        "point": 500
      },
      {
        "missionId": 3,
        "storeName": "반이학생마라탕",
        "storeCategory": "중식당",
        "missionTitle": "10000원 이상의 식사",
        "missionDate": "2025-09-20",
        "missionStatus": "COMPLETED",
        "point": 500
      },
      {
        "missionId": 4,
        "storeName": "반이학생마라탕",
        "storeCategory": "중식당",
        "missionTitle": "10000원 이상의 식사",
        "missionDate": "2025-09-20",
        "missionStatus": "COMPLETED",
        "point": 500
      }
    ]
  }
}
```