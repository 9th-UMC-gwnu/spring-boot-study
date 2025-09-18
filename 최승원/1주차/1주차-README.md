# 실습을 위한 로컬 docker-compose 설정
```yaml
services:
  mysql-db:
    image: mysql:8.0
    container_name: {CONTAINER_NAME}
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
      TZ: 'Asia/Seoul'
    ports:
      - "3307:3306"
    volumes:
      - ./data:/var/lib/mysql
    command:
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
    healthcheck:
      test: ["CMD", "mysqladmin" ,"ping", "-h", "localhost"]
      interval: 30s
      timeout: 1s
      retries: 5
```

# 요구 사항에 따른 쿼리문 작성

## 1번

### 요구 사항
> 성공 점수가 500 포인트 이상인 미션을 조회하고싶어요!

### 쿼리문
```sql
SELECT * FROM mission WHERE point >= 500;
```

---

## 2번(join 사용)

### 요구 사항
> 안암동의 미션들을 조회하고 싶어요!

### 쿼리문
```sql
SELECT * FROM mission    
LEFT JOIN store ON mission.store_id = store.store.id  
LEFT JOIN location ON store.location_id = location.location.id    
WHERE location.name = '안암동';
```

---

## 3번(서브 쿼리 사용)

### 요구 사항
> 별점이 3 이상인 리뷰 사진 URL들을 조회하고 싶어요!!

### 쿼리문(서브 쿼리 사용)
```sql
SELECT photo_url FROM review_photo as rp  
WHERE rp.review_id IN (   
    SELECT r.id FROM review as r  
    WHERE r.mission_id = 1    
);
```

### 쿼리문(left join 사용)
```sql
SELECT photo_url FROM review_photo as rp  
LEFT JOIN review as r ON rp.review_id = r.review_id   
WHERE review.star >= 3;
```

# Join과 Sub query의 차이

- Join은 두 개 이상의 테이블을 결합하는 데 사용된다.
- 서브 쿼리는 쿼리 내에서 다른 쿼리를 실행하는 데 사용된다.
- 서브 쿼리는 가독성이 좋지만 성능이 조인에 비해 매우 좋지 않다. 최신 MySQL은 사용자가 서브 쿼리문을 사용하면 자체적으로 조인문으로 변환하여 실행시키도록 업데이트되었다.
- 조인은 두 테이블의 데이터를 한번만 조회하는 반면에, 서브쿼리는 두 테이블의 데이터를 두번 조회하기 때문에, 조인이 일반적으로 서브쿼리보다 효율적이다.

## 살짝 복잡한 쿼리문 작성해보기
```sql
SELECT sum(point) FROM mission    
LEFT JOIN user_mission ON mission.id = user_mission.mission_id    
WHERE um.is_complete = 1 AND um.user_id = 1 AND m.deadline >= '2025-09-08';
```

# 페이징

- 데이터베이스 자체에서 끊어서 가져오는 것을 페이징이라고 한다.

## Offset based 페이징

### 쿼리문
```sql
SELECT * FROM mission
ORDER BY mission_id DESC
LIMIT 10 OFFSET 0;
```

#### 추론
```sql
SELECT * FROM mission
ORDER BY mission_id DESC
LIMIT y OFFSET (x - 1) * 1;
```

```sql
SELECT * FROM mission
ORDER BY mission_id DESC
LIMIT 15 OFFSET (n - 1) * 15;
```

## Offset 페이징의 단점

### 1. offset이 엄청 크면 다 조회해야 한다.

### 2. 데이터 정합성 문제

### Cursor based 페이징

### 함수 기반 커서
```sql
select r.*, CONCAT(LPAD(r.star, 10, '0'), LPAD(r.review_id, 10, '0')) as cursor_value
from review as r
where CONCAT(LPAD(r.star, 10, '0'), LPAD(r.review_id, 10, '0')) < '00000000030000000052'
order by r.star desc, r.review_id desc
limit 15;
```

### 복합 커서
```sql
select * from review as r
where r.star < ? or (r.star = ? and r.review_id < ?) # 4 / 4 / 733
order by r.star desc, r.review_id desc
limit 15;
```

## 함수 기반 인덱스

- 컬럼의 값이 아니라 함수나 표현식 결과를 저장하는 인덱스.

### example
```sql
-- 대소문자 구분 없이 검색하기 위한 인덱스
CREATE INDEX idx_user_lower_email
ON users (LOWER(email));
```

## 복합 인덱스

- 여러 컬럼을 묶어 인덱스로 만든 것.

### example
```sql
CREATE INDEX idx_user_name_email
ON users (name, email);
```

# 핵심 키워드

- DB Join이란?
  - 서로 다른 테이블의 행들을 특정한 조건으로 결합하여 하나의 결과 집합으로 나타내는 SQL 연산.
- Join 종류들
  - left join
  - right join
  - inner join
  - outer join
  - full join
  - cross join
  - self join
  - natural join
- 트랜잭션이란?
  - 하나의 논리적 작업의 단위. 작업은 모두 성공하거나, 모두 실패해야 한다.
- Join on 과 where의 차이
  - ON은 조인 조건을 정의한다 — 어떤 행끼리 매칭할지(조인 단계에서 적용). 
  - WHERE은 조인 결과에 대한 필터다 — 조인 이후의 결과 행을 걸러냄.

# 미션

## 설계한 ERD

## DDL 작성
```sql
# --- users ---
create table if not exists users
(
    id           bigint auto_increment primary key   not null comment '사용자 PK',
    name         varchar(20)                         not null,
    nickname     varchar(20)                         not null,
    gender       varchar(10)                         not null,
    status       varchar(10)                         not null,
    inactived_at timestamp                           not null,
    created_at   timestamp default CURRENT_TIMESTAMP not null comment '데이터 생성일자',
    updated_at   timestamp default CURRENT_TIMESTAMP on update CURRENT_TIMESTAMP comment '데이터 수정일자',

    constraint chk_gender
    check ( gender in ('남', '여') ),

    constraint chk_status
    check ( status in ('active', 'inactive'))
    );

# --- rents ---
create table if not exists rents
(
    id         bigint auto_increment primary key   not null,
    user_id    bigint                              not null,
    book_id    bigint                              not null,
    created_at timestamp default CURRENT_TIMESTAMP not null comment '데이터 생성일자',
    updated_at timestamp default CURRENT_TIMESTAMP on update CURRENT_TIMESTAMP comment '데이터 수정일자',

    constraint fk_rents_to_users foreign key (user_id) references users (id),
    constraint fk_rents_to_books foreign key (book_id) references books (id)
    );

# --- books ---
create table if not exists books
(
    id          bigint auto_increment primary key   not null,
    category_id bigint                              not null,
    title       varchar(50)                         not null,
    description varchar(500)                        not null,
    created_at  timestamp default CURRENT_TIMESTAMP not null comment '데이터 생성일자',
    updated_at  timestamp default CURRENT_TIMESTAMP on update CURRENT_TIMESTAMP comment '데이터 수정일자',

    constraint fk_books_to_categorys foreign key (category_id) references categorys (id)
    );

# --- categorys ---
create table if not exists categorys
(
    id         bigint auto_increment primary key   not null,
    name       varchar(20)                         not null,
    created_at timestamp default CURRENT_TIMESTAMP not null comment '데이터 생성일자',
    updated_at timestamp default CURRENT_TIMESTAMP on update CURRENT_TIMESTAMP comment '데이터 수정일자'
    );

# --- book_hash_tags ---
create table if not exists book_hash_tags
(
    id          bigint auto_increment primary key not null,
    book_id     bigint                            not null,
    hash_tag_id bigint                            not null,

    constraint fk_book_hash_tags_to_books foreign key (book_id) references books (id),
    constraint fk_book_hash_tags_to_tags foreign key (hash_tag_id) references hash_tags (id)
    );

# --- hash_tags ---
create table if not exists hash_tags
(
    id         bigint auto_increment primary key   not null,
    name       varchar(20)                         not null,
    created_at timestamp default CURRENT_TIMESTAMP not null comment '데이터 생성일자',
    updated_at timestamp default CURRENT_TIMESTAMP on update CURRENT_TIMESTAMP comment '데이터 수정일자'
    );

# --- alarms ---
create table alarms
(
    id          bigint auto_increment primary key   not null,
    user_id     bigint                              not null,
    type        varchar(20)                         not null,
    title       varchar(50)                         not null,
    description varchar(500)                        not null,
    created_at  timestamp default CURRENT_TIMESTAMP not null comment '데이터 생성일자',

    constraint chk_type check ( type in ('notice', 'marketing')),

    constraint fk_alarms_to_users foreign key (user_id) references users (id)
);
```
