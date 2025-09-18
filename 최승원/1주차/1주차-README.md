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
