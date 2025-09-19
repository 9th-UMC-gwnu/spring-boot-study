# 실전 SQL

## Join이란?
- 두 개 이상의 테이블을 결합하는데 사용

## Subquery란?
- 쿼리 내에서 다른 쿼리를 실행하는데 사용(서브 쿼리의 결과가 메인 쿼리의 실행으로 사용)

## Join과 Subquery의 차이
- Subquery는 쿼리 내에서 다른 쿼리 실행하는데 사용되지만 Join은 두 개 이상의 테이블을 결합하는데 사용
- Subquery는 가독성이 좋아 복잡한 쿼리를 작성할때 좋지만 성능이 Join에 비해 매우 떨어짐.

---

# 페이징(Paging)

## Offset based 페이징
- 직접 페이지 번호를 찾아내어 이동하는 페이징
- limit y offset(x - 1) * y; 같은 형식
- limit을 통해 한 페이지에 보여줄 데이터의 개수를 정하고
- offset을 통해 몇 개를 건너뛸지 정함.

### 단점
- offset이 크면 모두 조회
    - 예를 들어 offset이 1000이면 1000개의 데이터를 세고, 이후 limit만큼 조회
- 데이터 정합성 문제
    - 예를 들어 사용자가 페이지를 넘기는 와중에 다른 사용자가 데이터를 추가, 변경시 데이터 누락 발생 가능성 있음

## Cursor based 페이징
- 커서로 대상을 가르켜 이동하는 페이징
- 커서로 가르키는 대상은 항상 마지막 값
    - 조회 방식
        - 기본 속성과 PK키를 LPAD함수를 사용해 문자열 길이를 맞추고
        - CONCAT함수를 사용해 두 문자열을 연결하여 커서 값으로 사용하여 조회
            - 직관적이고
            - 커서를 하나로 관리할 수 있어 간편
            - EX)
                - select r.*, **CONCAT(LPAD(r.star, 10, '0'), LPAD(r.review_id, 10, '0'))** as cursor_value
                  from review as r
                  where **CONCAT(LPAD(r.star, 10, '0'), LPAD(r.review_id, 10, '0'))** < '00000000030000000052'
                  order by r.star desc, r.review_id desc
                  limit 15;

        - PK와 AND, OR 절을 이용하여 조회
            - 함수를 사용하지 않아 인덱스 사용이 용이하고
            - 함수에 대한 지식이 없어도 사용 가능
            - EX)
                - select * from review as r
                  where **r.star < ? or (r.star = ? and r.review_id < ?)** # 4 / 4 / 733
                  order by r.star desc, r.review_id desc
                  limit 15;

### 성능
- MySQL 8.X 버전 기준, 두 방식 모두 인덱스 활용이 가능
- CONCAT은 함수 기반 인덱스를, where문의 AND, OR절을 이용한 방식은 복합 인덱스를 이용
- 인덱스를 남용하면 오히려 성능이 저하

---

# 핵심 키워드

- DB Join이란?
    - 두 테이블의 공통 컬럼을 기준으로 연결해주는 쿼리
- Join 종류들
    - Inner Join : 테이블 간에 조건에 맞는 데이터만 연결
    - Left Join : 왼쪽 테이블을 기준으로 조건이 맞는 데이터를 반환
    - Right Join : 오른쪽 테이블을 기준으로 조건이 맞는 데이터를 반환
    - Full Outer Join : 두 테이블 데이터를 모두 포함하여 반환
    - Cross Join : 테이블 간 모든 경우의 수로 반환
- 트랜잭션이란?
    - 한번에 실행되어야 하는 작업(쿼리문)들의 묶음으로 모두 실행되거나 롤백 되는 것
    - 특징
        - 원자성 (Atomicity)
            - 트랜잭션은 전부 실행되거나 전부 롤백
        - 일관성 (Consistency)
            - 트랜잭션이 끝나면 DB는 항상 일관된 상태여야 한다.
        - 고립성 (Isolation)
            - 여러 트랜잭션이 같이 실행되어도 서로 간섭하지 않는다.
        - 지속성 (Durability)
            - 트랜잭션이 성공하면 DB에 반영된다.
- Join on과 where의 차이
    - on : 두 테이블을 연결하는 조건
    - where : 연결된 테이블을 필터링하는 조건 ( 최종 결과를 필터링 )

---

## 미션

### users테이블

  `CREATE TABLE IF NOT EXISTS USERS`

  `(`

  `user_id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,`

  `name VARCHAR(15) NOT NULL COMMENT '이름',`

  `nickname VARCHAR(15) NOT NULL COMMENT '닉네임',`

  `phone_num VARCHAR(11) NOT NULL COMMENT '전화번호',`

  `gender ENUM('F','M') NOT NULL COMMENT '성별',`

  `create_at DATETIME(6) NOT NULL DEFAULT *CURRENT_TIMESTAMP*(6) COMMENT '생성일자',`

  `update_at  DATETIME(6) NOT NULL DEFAULT *CURRENT_TIMESTAMP*(6) ON UPDATE *CURRENT_TIMESTAMP*(6) COMMENT '수정일자',`

  `status ENUM('active', 'inactive') NOT NULL DEFAULT 'active' COMMENT '상태표시',`

  `inactive_at DATETIME DEFAULT NULL COMMENT '비활성화 일자',`

  `count INT NOT NULL DEFAULT 0 COMMENT '해결한 미션 수',`

  `point INT NOT NULL DEFAULT 0 COMMENT '보유 포인트',`

  `like_food VARCHAR(10) NOT NULL COMMENT '선호 음식'`

  `);`

### missions 테이블

  `CREATE TABLE IF NOT EXISTS missions`

  `(`

  `mission_id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,`

  `name VARCHAR(15) NOT NULL COMMENT '미션 이름',`

  `create_at DATETIME(6) NOT NULL DEFAULT *CURRENT_TIMESTAMP*(6) COMMENT '생성일자',`

  `point INT NOT NULL COMMENT '미션 포인트',  shop_id INT NOT NULL`

  `);`

### question 테이블

  `CREATE TABLE IF NOT EXISTS question`

  `(`

  `id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,`

  `title VARCHAR(20) NOT NULL COMMENT '문의 제목',`

  `body TEXT NOT NULL COMMENT '문의 내용',  user_id int NOT NULL`

  `);`

### regions 테이블

  `CREATE TABLE IF NOT EXISTS regions`

  `(`

  `region_id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,`

  `name VARCHAR(15) NOT NULL COMMENT '지역 이름'`

  `);`

### review 테이블

  `CREATE TABLE IF NOT EXISTS review`

  `(`

  `review_id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,`

  `title VARCHAR(20) NOT NULL COMMENT '리뷰 타이틀',`

  `body TEXT NOT NULL COMMENT '리뷰 내용',`

  `star DOUBLE NOT NULL COMMENT '별점',  user_id INT NOT NULL,`

  `shop_id INT NOT NULL`

  `);`

### shops 테이블

  `CREATE TABLE IF NOT EXISTS shops`

  `(`

  `shop_id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,`

  `name INT NOT NULL COMMENT '가게 이름',`

  `region_id INT NOT NULL`

  `);`

### solved_tables 테이블

  `CREATE TABLE IF NOT EXISTS solved_missions`

  `(`

  `solved_id INT NOT NULL PRIMARY KEY  AUTO_INCREMENT,`

  `solved_at DATETIME(6) NOT NULL DEFAULT *CURRENT_TIMESTAMP*(6) COMMENT '해결 일자',`

  `user_id int NOT NULL COMMENT '해결한 유저',`

  `mission_id INT NOT NULL COMMENT '해결한 미션'`

  `);`

### 외래키 연결
    - solved_missions - users

      `ALTER TABLE solved_missions`

      `ADD CONSTRAINT FK_users_TO_solved_missions`

      `FOREIGN KEY (user_id) REFERENCES users (user_id);`

    - solved_missions - missions

      `ALTER TABLE solved_missions`

      `ADD CONSTRAINT FK_missions_TO_solved_missions`

      `FOREIGN KEY (mission_id) REFERENCES missions (mission_id);`

    - missions - shops

      `ALTER TABLE missions`

      `ADD CONSTRAINT FK_shops_TO_missions`

      `FOREIGN KEY (shop_id) REFERENCES shops (shop_id);`

    - shops - regions

      `ALTER TABLE shops`

      `ADD CONSTRAINT FK_regions_TO_shops`

      `FOREIGN KEY (region_id) REFERENCES regions (region_id);`

    - reviews - users

      `ALTER TABLE review`

      `ADD CONSTRAINT FK_users_TO_review`

      `FOREIGN KEY (user_id) REFERENCES users (user_id);`

    - reviews - shops

      `ALTER TABLE review`

      `ADD CONSTRAINT FK_shops_TO_review`

      `FOREIGN KEY (shop_id) REFERENCES shops (shop_id);`

    - questions - users

      `ALTER TABLE question`

      `ADD CONSTRAINT FK_users_TO_question`

      `FOREIGN KEY (user_id) REFERENCES users (user_id);`



---

# 학습후기
- 서브 쿼리는 인지하고 있었지만 Join문에 대해선 잘 모르고 있었는데 이 학습을 통해 서브 쿼리 뿐 만 아니라 Join에 대해 보다 깊이 알 수 있었다.
- DB에서 페이징이 이루어지는지 학습을 통해 처음 알게 되었고 함수나 연산이 이루어지면 인덱스를 사용하기 어렵다는 사실을 새롭게 알게 되었다.
- 처음 학습하는 내용이 많아져 부족함을 느끼고 더 열심히 하게 되는 것 같고 부족함을 많이 느꼈다.
