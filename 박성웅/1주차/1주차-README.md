# Chapter 1. 실전 SQL - 어떤 Query를 작성해야 할까?

## PM의 요구사항
`특정 미션 기한을 기준으로 제가 완료한 미션 점수의 총합을 알고싶어요!`
```sql
SELECT SUM(point) FROM mission AS m
LEFT JOIN user_mission AS um ON m.mission_id=um.mission_id
WHERE um.is_complete=1 AND um.user_id=1 AND m.deadline>='2025-09-08';
```

---

## 페이징
- Database 자체에서 끊어서 가져오는 것이다.

### Offset based 페이징
```sql
-- 직접 페이지 번호를 찾아내어 이동하는 페이징
-- (x-1)인 이유는 보통 1페이지가 첫 페이지이기 때문
SELECT * FROM mission
ORDER BY mission_id DESC
LIMIT y OFFSET (x-1)*y;
```

### Offset paging의 단점
- offset이 엄청 크면 다 조회해야 한다.
- 데이터 정합성 문제가 발생한다.

--- 

## Cursor based 페이징
```sql
-- 커서(마지막으로 조회한 컨텐츠) 로 무언가를 가르켜 페이징
SELECT * FROM review
WHERE review_id < ? # 마지막으로 조회한 ID
order by review_id DESC
LIMIT 15;

-- 별점 순 커서 페이징은 다음 페이지 호출 시, 항상 같은 결과가 호출되는 단점이 있다.
-- ID는 고유값이지만, 별점은 중복가능한 값이이므로 커서가 중복될 수 있기 때문
SELECT * FROM review
WHERE review.star < ? # 3
ORDER BY review.star DESC
LIMIT 15;
```

### 기본 속성+ PK키를 합친 문자열을 커서로 설정하여 조회하는 방식
1. 아이디 값과, 별점 정보를 각각 10글자 씩 맞춰 padding한다.
2. 아이디 값과 별점 정보를 가지는 커서 값을 만들어서, 테이블에 붙인다.
3. 현재 페이징의 커서 값을 구하고, 커서값을 기준으로 정렬한다.
4. 상위 15개를 가져온다.

```sql
-- 리뷰 별점 + ID로 커서 기반 페이지네이션
-- 직관적이고 간편하다
SELECT r.*, CONCAT(LPAD(r.star,10,'0'),LPAD(r.review_id,10,'0')) AS cursor_v
FROM review AS r
WHERE CONCAT(LPAD(r.star,10,'0'), LPAD(r.review_id,10,'0')) < '00000000030000
ORDER BY r.star DESC, r.review_id DESC
LIMIT 15;
```

### PK와 AND, OR 절을 이용해서 조회하는 방식
```sql
-- 함수를 사용하지 않으므로 별다른 지식 없이 사용 가능
-- 인덱스를 활용하기 용이하다
SELECT * FROM review AS r
WHERE r.star < ? OR (r.star = ? AND r.review_id < ?) # 4 / 4 / 733
ORDER BY r.star DESC, r.review_id DESC
LIMIT 15;
```

--- 

## 🎯핵심 키워드
- DB Join이란? : 두 개 이상의 테이블을 결합하여 데이터를 조회하는 연산
- Join 종류들 : Inner, Left, Right, LEft Outer, Right Outer, Full, Cross, Natural
- 트랜잭션이란? : 데이터베이스의 상태를 변경시키는 작업의 단위
- Join on과 WHERE의 차이 : Join on은 붙이기 , WHERE는 필터링

---

## 📢학습 후기
- 페이지 호출에 관한 각각의 장점과 단점을 배웠습니다.
- 커서 기반 페이지네이션에 대해서 다양한 예시를 볼 수 있어서 좋았습니다.

## ✅실습 체크리스트
- [x] 커서 기반 페이징 연습해보기

## ☑️실습 인증
- [x] 커서 기반 페이징 연습해보기
```sql
select * from review
where review_id < ?
order by review_id desc
limit 15;
```
<img width="650" height="200" src="https://github.com/user-attachments/assets/46318776-e8a6-4587-9c82-e0f85a18a912" /><br>
<img width="700" height="350" src="https://github.com/user-attachments/assets/d01ddaa8-95ad-4948-bc8c-845a0ebf7a9b" /><br>



## 🔥 미션
- 0주차 때 직접 설계한 데이터베이스를 토대로 아래의 화면에 대한 쿼리를 작성.
- 설계한 DB 사진(0주차 DB 수정 가능!)

  <img width="482" height="462" src="https://github.com/user-attachments/assets/c8995d26-dd2f-4655-8b83-6141f187c644" /><br>

- 본문
>아래 화면에 대한 쿼리가 시니어 미션 안에 있는 데이터베이스 DDL인지 몰라서
>둘 다 만들어보기로 했다.

## DDL
```sql
CREATE TABLE `food` (
    `food_id`  bigint NOT NULL
);

CREATE TABLE `user` (
    `user_id`  bigint NOT NULL,
    `name` varchar(255)    NOT NULL,
    `gender`   enum('MALE','FEMALE','NONE')   NULL   COMMENT 'MALE,FEMALE,NONE',
    `birth`    date   NOT NULL,
    `address`  enum('지역구')   NULL   COMMENT '지역구',
    `social_uid`   varchar(255)    NOT NULL   COMMENT 'OAuth UID',
    `social_type`  enum('KAKAO','NAVER','APPLE','GOOGLE')   NULL   COMMENT 'KAKAO,NAVER,APPLE,GOOGLE',
    `point`    int    NOT NULL,
    `email`    varchar(255)    NOT NULL,
    `phone_number` varchar(20)    NULL   COMMENT '010-xxxx-xxxx',
    `deleted_at`   timestamp  NULL,
    `updated_at`   timestamp  NOT NULL   DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE TABLE `user_food` (
    `user_food_id` bigint NOT NULL,
    `user_id`  bigint NOT NULL,
    `food_id` bigint NOT NULL
);

CREATE TABLE `term` (
    `term_id`  bigint NOT NULL,
    `name` enum('AGE','SERVICE','PRIVACY','LOCATION','MARKETING')   NULL
);

CREATE TABLE `user_term` (
    `user_term_id` bigint NOT NULL,
    `user_id`  bigint NOT NULL,
    `term_id` bigint NOT NULL
);

CREATE TABLE `user_mission` (
    `user_mission_id`  bigint NOT NULL,
    `is_complete`  bit(1)    NOT NULL   DEFAULT 0,
    `mission_id`  bigint NOT NULL,
    `user_id`  bigint NOT NULL
);

CREATE TABLE `mission` (
    `mission_id`   bigint NOT NULL,
    `deadline` date   NOT NULL,
    `conditional`  varchar(255)    NOT NULL,
    `point`    int    NOT NULL,
    `created_at`   timestamp  NOT NULL,
    `store_id` bigint NOT NULL
);

CREATE TABLE `store` (
    `store_id` bigint NOT NULL,
    `name` varchar(255)    NOT NULL,
    `manager_number`   varchar(20) NOT NULL,
    `detail_address`   varchar(255)    NOT NULL,
    `location_id`  bigint NOT NULL
);

CREATE TABLE `location` (
    `location_id`  bigint NOT NULL,
    `name` varchar(255)    NOT NULL
);

CREATE TABLE `review` (
    `review_id`    bigint NOT NULL,
    `content`  text   NOT NULL,
    `created_at`   timestamp  NOT NULL,
    `star` float  NOT NULL,
    `user_id`  bigint NOT NULL,
    `store_id` bigint NOT NULL
);

CREATE TABLE `review_photo` (
    `review_photo_id`  bigint NOT NULL,
    `photo_url`    varchar(255)    NOT NULL,
    `review_id`    bigint NOT NULL
);

CREATE TABLE `reply` (
    `reply_id` bigint NOT NULL,
    `content`  text   NOT NULL,
    `review_id`    bigint NOT NULL
);

-- PK & FK

-- PK 추가
ALTER TABLE `food` ADD CONSTRAINT `PK_FOOD` PRIMARY KEY (`food_id`);
ALTER TABLE `user` ADD CONSTRAINT `PK_USER` PRIMARY KEY (`user_id`);
ALTER TABLE `user_food` ADD CONSTRAINT `PK_USER_FOOD` PRIMARY KEY (`user_food_id`);
ALTER TABLE `term` ADD CONSTRAINT `PK_TERM` PRIMARY KEY (`term_id`);
ALTER TABLE `user_term` ADD CONSTRAINT `PK_USER_TERM` PRIMARY KEY (`user_term_id`);
ALTER TABLE `user_mission` ADD CONSTRAINT `PK_USER_MISSION` PRIMARY KEY (`user_mission_id`);
ALTER TABLE `mission` ADD CONSTRAINT `PK_MISSION` PRIMARY KEY (`mission_id`);
ALTER TABLE `store` ADD CONSTRAINT `PK_STORE` PRIMARY KEY (`store_id`);
ALTER TABLE `location` ADD CONSTRAINT `PK_LOCATION` PRIMARY KEY (`location_id`);
ALTER TABLE `review` ADD CONSTRAINT `PK_REVIEW` PRIMARY KEY (`review_id`);
ALTER TABLE `review_photo` ADD CONSTRAINT `PK_REVIEW_PHOTO` PRIMARY KEY (`review_photo_id`);
ALTER TABLE `reply` ADD CONSTRAINT `PK_REPLY` PRIMARY KEY (`reply_id`);

-- FK 추가
ALTER TABLE `user_food` ADD CONSTRAINT `FK_USER_FOOD_user_id_TO_USER_user_id` FOREIGN KEY (`user_id`) REFERENCES `user` (`user_id`);
ALTER TABLE `user_food` ADD CONSTRAINT `FK_USER_FOOD_food_id_TO_FOOD_food_id` FOREIGN KEY (`food_id`) REFERENCES `food` (`food_id`);
ALTER TABLE `user_term` ADD CONSTRAINT `FK_USER_TERM_user_id_TO_USER_user_id` FOREIGN KEY (`user_id`) REFERENCES `user` (`user_id`);
ALTER TABLE `user_term` ADD CONSTRAINT `FK_USER_TERM_term_id_TO_TERM_term_id` FOREIGN KEY (`term_id`) REFERENCES `term` (`term_id`);
ALTER TABLE `user_mission` ADD CONSTRAINT `FK_USER_MISSION_user_id_TO_USER_user_id` FOREIGN KEY (`user_id`) REFERENCES `user` (`user_id`);
ALTER TABLE `user_mission` ADD CONSTRAINT `FK_USER_MISSION_mission_id_TO_MISSION_mission_id` FOREIGN KEY (`mission_id`) REFERENCES `mission` (`mission_id`);
ALTER TABLE `mission` ADD CONSTRAINT `FK_MISSION_store_id_TO_STORE_store_id` FOREIGN KEY (`store_id`) REFERENCES `store` (`store_id`);
ALTER TABLE `store` ADD CONSTRAINT `FK_STORE_location_id_TO_LOCATION_location_id` FOREIGN KEY (`location_id`) REFERENCES `location` (`location_id`);
ALTER TABLE `review` ADD CONSTRAINT `FK_REVIEW_user_id_TO_USER_user_id` FOREIGN KEY (`user_id`) REFERENCES `user` (`user_id`);
ALTER TABLE `review` ADD CONSTRAINT `FK_REVIEW_store_id_TO_STORE_store_id` FOREIGN KEY (`store_id`) REFERENCES `store` (`store_id`);
ALTER TABLE `review_photo` ADD CONSTRAINT `FK_REVIEW_PHOTO_review_id_TO_REVIEW_review_id` FOREIGN KEY (`review_id`) REFERENCES `review` (`review_id`);
ALTER TABLE `reply` ADD CONSTRAINT `FK_REPLY_review_id_TO_REVIEW_review_id` FOREIGN KEY (`review_id`) REFERENCES `review` (`review_id`);
```

```text
미션
          500p                            성공
가게 이름a 에서 12,000원 이상의 식사를 하세요!
            리뷰남기기
```
```sql
SELECT
    m.point,
    m.conditional,
    s.name
FROM user_mission AS um
JOIN mission AS m ON um.mission_id = m.mission_id
JOIN store AS s ON m.store_id = s.store_id
WHERE um.user_id = (자유) AND um.is_complete = 1; --완료
```
<img width="522" height="55" src="https://github.com/user-attachments/assets/e72b76f2-92e5-4753-a4f7-1548671f9bd4" /><br>



## ⚡트러블 슈팅

`이슈` <br>
- review 테이블 탐색이 안된다. <br>

`문제` <br>
- 외래키 user_id에 해당하는 컬럼값이 비어 있어서 탐색이 되지 않는 것이다. <br>

`해결` <br>
- 빈 user 테이블에 더미 데이터를 추가해준다. <br>

`참고레퍼런스` <br>
- https://m.blog.naver.com/o12486vs2/221995584623 <br>
