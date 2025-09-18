0주차 - Database 설계
===
목차
---
* 데이터베이스에 대하여
  * 데이터 베이스의 역사
  * SQL
  * NoSQL
* ERD
  * ERD란?
  * ERD 설계 실제로 해보기
* SQL 키워드
  * 키워드 정리
  
데이터베이스에 대하여
---
### 데이터베이스의 역사
코딩 어르신 시절, 지금처럼 관계형, NoSQL 이야기가 나오기 훨씬 이전에, 데이터는 단순히 파일로서 저장되었습니다.  
  
텍스트, 바이너리, CSV등등과 같이 저장하였고 실제로 적은 규모의 시스템이면 이러한 운영 방식도 코드만 잘 작성한다면 그렇게 크게 문제가 되지는 않을 것입니다.  
  
그러나 문제는 규모가 커지면서 발생하게 되는데, 여러 파일에 중복된 정보가 존재하고, 파일 구조에 따라 프로그램도 바꾸어야 해서 데이터 입출력 프로그램의 표준도 존재하지 않았으며, 데이터 삭제 및 삽입시의 데이터 자체의 무결성 보장 이슈, 동시에 접근하는 등의 상황 발생을 통한 파일 및 데이터의 손상 불일치 등등... 관계형 데이터베이스가 등장하기 전의 데이터 저장은 그야말로 혼돈의 도가니 그 자체였습니다.  
  
그러던 도중 행과 열을 통해 데이터를 저장하고 표현하는, 주제에 따라 분리되고 서로 종속되는 테이블, 그리고 그 테이블 항목의 유일한 이름을 키로서 서로 항목의 키를 가지는 형식을 통해 테이블과 그 관계를 통해 데이터 생태계를 운영하는 개념의 관계형 DB가 등장하면서 그 판도가 바뀌게 됩니다.  
  
### SQL
신뢰도가 적당히 있을지도 모르는 나무위키발 정보에 의하면, SQL은
  
> (Structured Query Language)의 약어로 1974년 IBM의 연구원이었던 도널드 체임벌린과 레이먼드 보이스가 개발하여 시퀄(SEQUEL)이라는 이름으로 발표한 언어가 그 시초이다.
  
라고 말하는데 말 그대로 구조화된, 관계형 데이터베이스를 조작하고 관리하기 위해 나온 대화형 언어입니다.  
  
실제로 인터프리터 방식으로 동작하며, 아주 큰 틀로 바라보자면 데이터베이스 로그인, 데이터베이스 유저 생성 및 삭제, 데이터베이스 생성 및 삭제, 테이블 생성 및 삭제, 테이블에 데이터 삽입 및 삭제라는 큰 틀로 이루어져 있습니다.  
  
실제로도 '어느 테이블의 어느 값을 참조하는 어느 테이블 항목을 업데이트하라' 와 같이 명령형으로 데이터베이스를 다룰 수 있게 해주는 간편함 또한 가지고 있으며, 명령만 똑바로 해주면 데이터베이스 내부의 효율적인 탐색, 데이터 무결성 보장 등등이 어느 관계형 데이터베이스에서도 보장됩니다.  
  
실제 언어 형식은 조금 뒤에서 서술하도록 하겠습니다.
  
### NoSQL
NoSQL은 (Not Only SQL)의 약어로 SQL, 즉 관계형 데이터베이스가 가지고 있는 문제점을 해결하기 위해 등장한 스케일과, 유연성을 핵심으로서 등장한 SQL입니다.  
  
관계형 데이터베이스에서 새로운 확장을 하려면, 새로운 '테이블'을 생성하고 그 테이블을 기존의 데이터베이스 시스템 내에 관계를 파악하여 넣어주어야 하며 단순히 컬럼을 늘리는 행위 또한, 전체 틀을 밖 어야 하는 등, 강력한 무결성 보장 및 종속성을 대가로 유연성을 잃어버렸습니다.  
  
데이터의 추가는 정형적인것만 존재하지 않으며 유연한 비정형 데이터를 수집하고 관리하기 위하여 NoSQL이 등장하게 되었습니다.  
  
NoSQL의 종류는 여러가지가 존재하는데, Key:Value 형식으로서만 묶고  초고속 캐싱과 세션 관리를 통해 저장하는 Redis, DynamoDB나,  
  
JSON 기반 파일을 생성하고 그 해당 항목을 key:value 형식으로 저장하여 어느정도 틀이 있지만 유연한 데이터베이스 스키마를 가질 수 있는 MongoDB 등등,  
  
유연한 스키마와 관계형을 조합한 GraphQL등 그 형태는 매우 다양하지만 그래도 그 특성은 확장을 위한 유연함과 다양성에 핵심이 있다는 것을 엿볼 수 있습니다.  
  
ERD
---
### ERD란?
관계형 데이터베이스의 관계와 컬럼을 시각화해서 데이터베이스 내 데이터 흐름을 표현한 도식도로서 데이터베이스의 데이터 흐름과 그 구조를 한눈에 알아보기 쉽다는 장점을 가지고 있습니다.  

``` mermaid
erDiagram
    STUDENT {
        int student_id PK
        string name
        string major
    }

    COURSE {
        int course_id PK
        string title
        int credits
    }

    ENROLLMENT {
        int enrollment_id PK
        int student_id FK
        int course_id FK
        string semester
    }

    STUDENT ||--o{ ENROLLMENT : ""
    COURSE  ||--o{ ENROLLMENT : ""
```

실제로는 조금씩 다르지만 핵심 개념은 거의 비슷합니다. 각 테이블에 대하여 PK와 널 여부를 지정하고 컬럼 항목을 작성 후, 해당 테이블간의 몇대몇 관계인지를 표시하고 그에 따른 포린키 컬럼들 또한 적어줍니다.  
  
물론, 최대한 확장성 있게 설계하겠지만, 위에서도 설명하였듯 관계형 데이터베이스는 그 형태가 한번 정해지고 운영되면 그 위에 새로운 틀을 추가하거나 수정하는것이 서비스가 커지면 커질수록 그리 쉽지 않은 편이며, 최적화는 더욱 어렵습니다.  분산 저장은 말할 것도 없습니다.
  
따라서 전체 데이터베이스 설계, 즉, ERD를 시작과 동시에 설계하는것이 좋고, 세세한 내용들은 프로젝트를 진행하면서 계속 수정하여서 실제 서비스 배포 전에 그 스키마를 완성하는 것을 목표로 지향을 하여야 합니다.  
  
당연한 이야기지만 팀 모두가 같은 ERD를 토대로 설계해야 할 것입니다.  
  
### ERD 설계 실제로 해보기
저는 다음과 같은 시나리오를 구상하여 진행해 보았습니다.  
#### 시나리오
---
최근에 정보가 끔찍하게 털려버린 에스24를 대체할 새로운 온라인 서점계의 신생 주자가 되기 위해 온라인 서점 서비스를 구상하고자 합니다.  

#### 요구사항
---
1. 하나의 사용자는 여러 인증수단을 연결할 수 있는데 로컬 자격과 카카오 인증이 있습니다
2. 상품 카탈로그는 books(작품 메타)와 book_items로 분리합니다.
3. 배송 주소는 addresses로 분리하고 주문 시 참조합니다.
4. 리뷰는 사용자와 도서에 연결하며 구매자 검증은 선택적으로 order_item_id로 연결해 확장 가능합니다.
5. 위시리스트로 관심 도서를 관리합니다.
6. 세션/토큰과 로그인 이력 등은 보안 및 감사 용도로 기본만 둡니다.

``` mermaid
erDiagram
  USERS {
    BIGSERIAL id PK
    VARCHAR email
    VARCHAR display_name
    VARCHAR status
    TIMESTAMP created_at
  }

  AUTH_IDENTITIES {
    BIGSERIAL id PK
    BIGINT user_id FK
    VARCHAR provider
    VARCHAR provider_user_id
    TIMESTAMP created_at
  }

  PASSWORD_CREDENTIALS {
    BIGSERIAL id PK
    BIGINT user_id FK
    VARCHAR login_id
    VARCHAR password_hash
    TIMESTAMP created_at
  }

  BOOKS {
    BIGSERIAL id PK
    VARCHAR title
    TEXT description
    TIMESTAMP created_at
  }

  BOOK_ITEMS {
    BIGSERIAL id PK
    BIGINT book_id FK
    VARCHAR isbn
    NUMERIC price
    INT stock_qty
  }

  CARTS {
    BIGSERIAL id PK
    BIGINT user_id FK
    TIMESTAMP created_at
    TIMESTAMP updated_at
  }

  CART_ITEMS {
    BIGSERIAL id PK
    BIGINT cart_id FK
    BIGINT book_item_id FK
    INT qty
  }

  ORDERS {
    BIGSERIAL id PK
    BIGINT user_id FK
    VARCHAR status
    NUMERIC total_amount
    TIMESTAMP created_at
  }

  ORDER_ITEMS {
    BIGSERIAL id PK
    BIGINT order_id FK
    BIGINT book_item_id FK
    INT qty
    NUMERIC price
  }

  REVIEWS {
    BIGSERIAL id PK
    BIGINT user_id FK
    BIGINT book_id FK
    INT rating
    TEXT content
    TIMESTAMP created_at
  }

  %% Relationships
  USERS ||--o{ AUTH_IDENTITIES : has
  USERS ||--o| PASSWORD_CREDENTIALS : has
  BOOKS ||--o{ BOOK_ITEMS : contains

  USERS ||--o{ CARTS : owns
  CARTS ||--o{ CART_ITEMS : includes
  BOOK_ITEMS ||--o{ CART_ITEMS : referenced_by

  USERS ||--o{ ORDERS : places
  ORDERS ||--o{ ORDER_ITEMS : contains
  BOOK_ITEMS ||--o{ ORDER_ITEMS : referenced_by

  USERS ||--o{ REVIEWS : writes
  BOOKS ||--o{ REVIEWS : receives
```

여기서는 머메이드 코드로 나타내었습니다만 실제로는 각종 ERD 작성 툴을 이용해서 작성하시면 좋을 것입니다.  

SQL 키워드
---
### 키워드 정리
우선 여기서는 관계형 데이터베이스에서 중요하게 쓰이는 용어들과 SQL 기본 명령어들을 적음으로서 키워드를 작성하겠습니다.  
  
#### 기본 키워드

| 용어               | 설명                                                             |
| ---------------- | -------------------------------------------------------------- |
| PK (Primary key) | 테이블의 각 행을 고유하게 식별하는 키로서 중복이 불가능과 NULL 불가의 특징을 가지고 있습니다.        |
| FK (Foreign Key) | 다른 테이블의 PK를 참조하는 키로서 테이블 관 연관관계를 표현하는데 사용됩니다.                  |
| UK (Unique Key)  | 컬럼 값의 중복을 허용하지 앟는 제약 조건으로서 NULL은 허용하는 특성이 있습니다. PK와는 다른 개념입니다. |
| INDEX            | 검색 성능을 높이기 위해 특정 컬럼에 부여하는 자료 구조입니다.                            |
| NOT NULL         | 해당 컬럼에 반드시 값이 있어야 한다는 제약 조건입니다.                                |
| AUTO INCREMENT   | 행이 추가될 때 자동으로 증가하는 숫자 값을 의미합니다                                 |
| CHECK            | 컬럼 값이 특정 조건을 만족해야 한다는 제약조건입니다.                                 |
| ENUM             | 미리 정의한 값만 허용하는 자료형입니다.                                         |
| DEFAULT          | 값이 입력되지 않았을 경우 자동으로 채워지는 기본값입니다.                               |

#### 관계

| 용어       | 설명                                                                                                                     |
| -------- | ---------------------------------------------------------------------------------------------------------------------- |
| 1 : 1 관계 | 한 테이블의 한 행이 다른 테이블의 한 행과만 연결됩니다.                                                                                       |
| 1 : N 관계 | 한 테이블의 한 행이 다른 테이블의 여러 행과 연결되고, 보통 N 쪽이 Fk를 가지는 형태로 구현됩니다.                                                             |
| N : M 관계 | 두 테이블이 다대다 관계를 가질때 표현하는 방식으로 조인 테이블을 만들어 연결합니다. 즉, 하나의 N:M 관계는 각 테이블에 대해 1:N N:1이 성립되는 또 하나의 테이블을 만드는 것으로 해결할 수도 있습니다. |

#### SQL

| 용어                               | 설명                                                          |
| -------------------------------- | ----------------------------------------------------------- |
| DDL (Data Definition Language)   | 데이터 구조 정의어로서 **CREATE, ALTER, DROP** 등의 명령어가 존재합니다.         |
| DML (Data Manipulation Language) | 데이터 조작어로서 **INSERT, UPDATE, DELETE, SELECT** 등의 명령어가 존재합니다. |
| DCL (Data Control Language)      | 데이터베이스 권한 제어를 위한 언어로서, **GRANT, REVOKE** 등의 명령어가 존재합니다.     |
  
후기
---
이전에 저는 교수님에게서 이런 말을 들었던 적이 있습니다.
  
> "정말 제대로 이해했는지 확인하고 싶다면 남들에게 그 내용을 설명해봐라"
  
이 말을 깊게 체감하며 이번 기회를 통해 기존에 알고 있던 내용을 한번 더 정리하면서 깔끔해진 느낌입니다.
  
기존에 안다고 착각했던 내용 또한 다시 한번 조사하고 머리속에서 정리하는 과정을 통해 정리할 수 있는 기회가 되었던것 같습니다. 지금도 실감이 나지만 아직도 모르는게 많습니다. 실제로 저는 아직 쿼리에 대한 이해도가 그렇게 높은 편이 아니고 데이터베이스 지식도 많이 부족한 상황입니다.  
  
늘 그래왔듯 기초가 탄탄해야 이후 지식을 습득할 때 더 효율적으로 습득할 수 있는 근거가 되리라고 믿고 있습니다. 따라서 이번 기회를 통해 제 생각 정리와 더불어, 더욱 더 생각을 확장할 수 있는 기반을 다지고자 합니다.
  
이번 UMC가 그 기회가 되었으면 합니다.