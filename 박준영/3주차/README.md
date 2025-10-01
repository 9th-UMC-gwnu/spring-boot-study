# API URL의 설계 & 프로젝트 세팅

## API란 ?
- 어플리케이션을 프로그래밍 할 때, 보다 쉽게 할 수 있도록 해주는 도구

## REST API
- HTTP를 기반으로 하는 웹 서비스 아키텍처
- HTTP 메서드와 자원을 활용해 서로 간의 통신을 주고 받는 방식

## HTTP 메소드

### GET 
- 멱등성의 개념을 가지고 있어, 여러 번 조회를 하여도, 리소스는 변화 X
- 서버에 데이터를 전달하는 경우 쿼리 스트링을 이용 -> 무방비할 수 있어 유의
- 바디를 이용한 데이터 전달도 가능하지만 지원하지 않는 곳이 많아 권장 X

### POST
- Post 요청은 데이터를 처리하고, 바디를 통해 데이터를 전달
- *주로 신규 리소스를 생성*하고, 등록하고, 업데이트하는데 사용
- 쿼리 파라미터는 키와 밸류 형식으로 구성

### PUT
- 리소스를 완전히 대체하는 개념
- 리소스가 있으면 업데이트, 없으면 생성
- 멱등성 보장

### PATCH
- 멱등성을 지니지 않아 리소스를 부분 수정하며 값을 변경
- SOFT DELETE 경우에도 사용 (삭제된 것 처럼 상태 변경)

### DELETE
- 리소스를 제거할 때 사용한다.
- HARD DELETE 경우 사용 (데이터 실제 삭제)

#### 멱등성이란?
- 동일한 연산을 여러 번 수행해도 결과가 달라지지 않는 성질


---

## API Endpoint
- API가 서버에서 리소스에 접근할 수 있게 해주는 URL
  - URL -> Scheme:// Host :Port /Path ?Query #Fragment
    - 스키마(Scheme) : 통신 프로토콜을 의미
    - 호스트(Host) : 서버의 도메인 이름 또는 IP 주소를 의미
    - 포트(Port) : 자원에 접근하기 위한 번호를 의미, 생략 가능
    - 패스(Path) : 서버내에 자원이 위치하는 경로를 의미
    - 쿼리(Query) : 자원에 대한 추가 질의를 의미, 키 = 밸류 형식
    - 프라그먼트(Fragment) : 자원의 특정 부분을 가르키는 값, 서버에 전송 X
    - 

## RESTFUL API Endpoint 규칙
1. URI에 **동사가 포함이 되어선 안된다.**
   - EX) /users O, /getUsers X 
2. URI에서 **단어의 구분이 필요한 경우 -(하이픈)을 이용**한다.
   - EX) /user-profile O, /userProfile X
3. **자원**은 기본적으로 **복수형으로 표현**한다.
   - EX) /users O, /user X
4. 단 하나의 자원을 **명시적으로 표현**을 하기 위해서는 **/users/id와 같이 식별 값을 추가로 사용**한다.
   - EX) /users/{id}
5. **자원 간 연관 관계가 있을 경우 이를 URI에 표현한다.**
   - EX) /users/{userId}/posts

### 리소스간 연관 관계가 있는 경우, N:M의 경우 설계
- 리스소간 연간 관계가 있는경우
  - 예를 들어 교과목이 **한 명의 사용자(교사)가 여러 개의 교과목을 강의 할 수 있다**고 하고,
  - 교과목 하나 단건 조회 API를 설계한다면?
    - 특정 교사의 특정 교과목이 더 의미가 잘 전달이 되는지 -> /users/id/subjects/id 
    - 아니면 그저 특정 교과목이 더 의미가 잘 전달이 되는지 -> /users/subjects/id
- N:M의 경우
  - 비즈니스 상에서 더 중요한 대상을 더 앞 계층에 놓음
    - EX) 게시물과 해시태그의 경우 -> /artcles/hash-tag (게시물 > 해시태그)


---


## 세부적인 API 설계

### Path Variable
- URL 경로의 일부로 특정 리소스를 식별하는 값
- 단 하나, 특정 자원을 지정할 때 사용
  - EX) GET /users/articles/id -> id는 게시글의 기본키를 의미

### Query String
- 여러 개의 자원을 지정할 때 사용
- 검색 조회를 의미
  - EX) GET GET /users/articles?name=umc&owner=ddol

### Request Body
- 데이터를 URL에 노출되지 않게 전송할 때 사용
- POST 방식에서 json, form-data 형식으로 전달
  - EX) json {
    "name" : "김주헌",
    "phoneNum" : "010-1111-2222",
    "nickName" : "mark",
    }

### Request Header
- 전송에 관련된 기타 정보들이 담기는 부분
- Body의 형식이 무엇인지, 인증 정보, 요청을 보낸 곳(Origin) 등의 정보를 담음


---


# 미션

## 홈 화면
- Endpoint: GET /api/home
- Request Header: Authorization: Bearer <JWT_TOKEN>, Content-Type: application/json
- Query String : ?limit=5 -> 최근 미션 5개만 보기
  - Response Body : {       -> 보유 미션이 3개라 3개 출력
    "message": "안녕하세요! 홍길동님",
    "myMissions": 
    [
      { 
        "mission_id": 1, 
        "storeName": "명륜진사갈비",
        "name": "4인 이상 식사하러 가기", 
        "status": "IN_PROGRESS",
        "point": 700 
      },
      { 
        "mission_id": 2,
        "storeName": "금룡", 
        "name": "1인 1메뉴 주문하기",
        "status": "COMPLETED",
        "point": 500 
      },
      {
        "mission_id": 3,
        "storeName": "좋아회",
        "name": "매운탕 주문하기",
        "status": "COMPLETED",
        "point": 300
      }
    ]
  }

## 마이페이지 
- Endpoint: GET /api/users/my_profile
- Request Header
- Authorization: Bearer <JWT_TOKEN>, Content-Type: application/json
- Path Variable :
- Response Body: {
      "userId": 1,
      "username": "홍길동",
      "email": "honggd@gmail.com",
      "myMissions": [
        {
          "mission_id": 1,
          "storeName": "명륜진사갈비",
          "name": "4인 이상 식사하러 가기",
          "status": "IN_PROGRESS",
          "point": 700
        },
        {
          "mission_id": 2,
          "storeName": "금룡",
          "name": "1인 1메뉴 주문하기",
          "status": "COMPLETED",
          "point": 500
        },
        {
          "mission_id": 3,
          "storeName": "좋아회",
          "name": "매운탕 주문하기",
          "status": "COMPLETED",
          "point": 300
        }
      ],
      "reviews": [
        {
          "storeName": "금룡",
          "star" : 5
          "comment" : "너무 맛있어요 !",
          "photo": [image_file]
        }
      ]
  }

## 리뷰 작성
- Endpoint: POST /api/shops/{shop_id}/reviews
- Request Header: Authorization: Bearer <JWT_TOKEN>, Content-Type: application/json
- Query String :  
  - Request Body : {
      "storeName": "금룡",
      "star" : 5
      "comment" : "너무 맛있어요 !",
      "photo": [image_file]
  }

## 미션 목록 조회(진행중)
- Endpoint: GET /api/users/{user_id}/missions
- Request Header: Authorization: Bearer <JWT_TOKEN>, Content-Type: application/json
- Query String : ?status= IN_PROGRESS
  - Response Body : {
    "myMissions":
    [
      {
        "mission_id": 1,
        "storeName": "명륜진사갈비",
        "name": "4인 이상 식사하러 가기",
        "status": "IN_PROGRESS",
        "point": 700
      }
    ]
  }

## 미션 목록 조회(진행완료)
- Endpoint: GET /api/users/{user_id}/missions
- Request Header: Authorization: Bearer <JWT_TOKEN>, Content-Type: application/json
- Query String : ?status= COMPLETED
  - Response Body : {
      "myMissions":
        [
          {
            "mission_id": 2,
            "storeName": "금룡",
            "name": "1인 1메뉴 주문하기",
            "status": "COMPLETED",
            "point": 500
          },
          {
            "mission_id": 3,
            "storeName": "좋아회",
            "name": "매운탕 주문하기",
            "status": "COMPLETED",
            "point": 300
          }
      ]
    }

## 미션 성공 누르기
- Endpoint: POST /api/missions/{mission_id}/complete
- Request Header: Authorization: Bearer <JWT_TOKEN>, Content-Type: application/json
- Query String : 
- Response Body : {
    "mission_id": 4,
    "message" : "미션 성공 !",
    "storeName": "깡통", 
    "name": "막창 주문하기"
  }

## 회원가입 하기
- Endpoint: GET /api/users/signup
- Request Header: Authorization: Bearer <JWT_TOKEN>, Content-Type: application/json
- Query String : 
- Response Body : {
      "name": "홍길동",
      "nickname": "GD",
      "gender": "M",
      "phone_num": "010-1234-5678",
      "birth": "1999-01-01",
      "address": "강원도 원주시 흥업면"
  }


---


## 후기
- API Endpoint 지정할 때 이렇게 세부적으로 설계하는지 몰랐는데 이번 학습을 통해 이런 규칙과 세부사항이 있음을 알아가는 뜻 깊은 시간이 되었다.
- 또한 HTTP 메서드에서 어떤 메서드를 어떤 경우에 사용하는지 예를 들어 PUT 으로 업데이트를 하면 모든 리소스를 가져오기 때문에 비효율적인 경우가 많았는데 PATCH 메서드로 대체할 수 있다는 점 등 많은 것을 알아갈 수 있었다.