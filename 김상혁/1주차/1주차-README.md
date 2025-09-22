# 실전 SQL
## Join이란?
- 두 개 이상의 테이블을 공통된 키을 기준으로 연결해서 한 번에 데이터를 가져오는 방법이다.
## Subquery란?
- 쿼리 안에 또 다른 쿼리를 넣어서, 그 결과를 이용하는 방식이다.
## Join과 Subquery의 차이
- 조인은 두 개 이상의 테이블을 결합하는 데 사용되는 반면, 서브 쿼리는 쿼리 내에서 다른 쿼리를 실행하는데 사용됩니다.
- 서브쿼리는 가독성이 좋지만 성능이 조인에 비해 매우 좋지 않습니다. 조인은 두테이블의 데이터를 한번만 조회하고 서브쿼리는 두번 조회합니다.
- 최신 mysql에서는 서브쿼리문을 자체적으로 조인문으로 변환하여 실행시킵니다.
---
# 페이징(Paging)
## Offset based 페이징
- 직접 페이지 번호를 찾아내어 이동하는 페이징이다.
- (limit y offset(x - 1) * y;)같은 형식으로 쓰인다.
### 단점
- offset이 큰 수 : offset이 만약 큰 수면 그만큼 다 조회한뒤 limit만큼 조회해야 한다.
- 데이터 정합성 문제 : 사용자가 1페이지에서 2페이지로 넘어가려는 순간, 게시글이 추가가 되면 1페이지에 새 게시글이 추가되고 2페이지에 기존에 봤던 게시글이 와서 중복된 데이터를 보게 된다.
## Cursor based 페이징
- 커서로 무언가를 가르켜 페이징을 하는 방법이다.
- 중복되는 값을 커서로 지정하면 조회시 같은 결과가 호출 될 수 있으므로 중복될 수 없는 키를 커서에 추가해야 한다.
  - 기본 속성 + PK키를 합친 문자열을 커서로 설정하는 방식은 직관적이고 커서를 하나로 관리 할 수 있다는 장점이 있다.
  - PK와 AND, OR 절을 이용해서 조회하는 방식의 장점은 함수를 사용하지 않아서 별다른 지식없이 사용 가능하고 인덱스를 활용하기 용이하다.
### 성능
- MySQL 8.X 버전 기준, 두 방식 모두 인덱스 활용이 가능합니다. Concat은 함수 기반 인덱스를, where문의 AND, OR절을 이용한 방식은 복합 인덱스를 이용하면 됩니다.
- 인덱스를 남용하면 오히려 성능이 떨어집니다. 
---
# 핵심 키워드
- DB Join이란? : DB Join은 두 개 이상의 테이블을 연결하여 관련된 데이터를 조회하는 방법
- Join 종류들
   - Inner Join : 두 테이블 간에 일치하는 데이터만 반환한다.
   - Outer Join
     - Left Outer Join : 왼쪽 테이블(처음 언급된 테이블) 데이터와 두 테이블 간에 일치하는 데이터를 반환한다.
     - Right Outer Join : 위와 반대로 오른쪽 테이블 데이터와 일치하는 데이터를 반환한다.
     - Full Outer Join : 두 테이블에 모든 행을 반환한다.
   - Cross Join : 두 테이블의 카테시안 곱을 반환한다. 서로 모든 행을 매치시킨다.
   - Self Join : 같은 테이블의 내용을 비교 할 때 자기 자신과 조인을 한다.
- 트랜잭션이란? : DB에서 한번에 실행되어야 하는 작업들의 묶음으로 모두 실행되거나 전부 취소된다.
  - 특징
    - 원자성 (Atomicity)
      - 트랜잭션은 전부 실행되거나 전부 취소된다.
    - 일관성 (Consistency)
      - 트랜잭션이 끝나면 DB는 항상 일관된 상태여야 한다.
    - 고립성 (Isolation)
      - 여러 트랜잭션이 같이 실행되어도 서로 간섭하지 않는다.
    - 지속성 (Durability)
      - 트랜잭션이 성공하면 DB에 반영된다.
- Join on과 where의 차이 : Join on은 여러 테이블을 연결할 때 조건으로 특정 기준으로 합치는지 결정하는 것이고 where은 하나의 테이블이나 조인된 결과의 테이블에서 다시 특정 기준으로 필터링해서 특정 데이터만 남긴다.

---
# 미션
## 지역 테이블
CREATE TABLE location (
    id bigint NOT NULL PRIMARY KEY,
    name varchar(10)
);
## 유저 테이블
CREATE TABLE user (
    id bigint NOT NULL PRIMARY KEY,
    name varchar(10) NOT NULL,
    nickname varchar(10),
    email varchar(20),
    birth varchar(10),
    gender ENUM('m', 'w') NOT NULL,
    point int,
    created_at datetime,
    updated_at datetime,
    inactived_at datetime,
    status enum('active','inactive') NOT NULL
);
## 가게 테이블
CREATE TABLE store (
    id bigint NOT NULL PRIMARY KEY,
    name varchar(20) NOT NULL,
    information varchar(50),
    address varchar(30),
    location_id bigint NOT NULL,
    CONSTRAINT fk_store_location FOREIGN KEY (location_id) REFERENCES location(id)
);
## 미션 테이블
CREATE TABLE mission (
    id bigint NOT NULL PRIMARY KEY,
    details varchar(50),
    deadline datetime,
    point int NOT NULL,
    store_id bigint NOT NULL,
    CONSTRAINT fk_mission_store FOREIGN KEY (store_id) REFERENCES store(id)
);
## 리뷰 테이블
CREATE TABLE review (
    id bigint NOT NULL PRIMARY KEY,
    content text,
    star float NULL,
    created_at datetime,
    user_id bigint NOT NULL,
    store_id bigint NOT NULL,
    CONSTRAINT fk_review_user FOREIGN KEY (user_id) REFERENCES user(id),
    CONSTRAINT fk_review_store FOREIGN KEY (store_id) REFERENCES store(id)
);
## 유저 미션 테이블
CREATE TABLE user_mission (
    id bigint NOT NULL PRIMARY KEY,
    is_success boolean,
    mission_id bigint NOT NULL,
    user_id bigint NOT NULL,
    CONSTRAINT fk_user_mission_mission FOREIGN KEY (mission_id) REFERENCES mission(id),
    CONSTRAINT fk_user_mission_user FOREIGN KEY (user_id) REFERENCES user(id)
);
## 선호 지역 테이블
CREATE TABLE location_likes (
    location_id bigint NOT NULL,
    user_id bigint NOT NULL,
    PRIMARY KEY (location_id, user_id),
    CONSTRAINT fk_location_likes_location FOREIGN KEY (location_id) REFERENCES location(id),
    CONSTRAINT fk_location_likes_user FOREIGN KEY (user_id) REFERENCES user(id)
);

# 학습후기
- 조인과 서브쿼리의 차이를 잘 이해하지 못했는데 성능차이와 가독성차이가 있다는 것을 알게 되었고, mysql에서 서브쿼리를 쓰면 자동으로 조인으로 바꿔준다는 것도 처음알았다.
- 페이징은 오프셋 페이징말고도 더 좋은 커서 페이징이 있다는 것을 배웠고, 실제로 어떻게 작성하는지 이해했다.
