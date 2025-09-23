# Query

## Query란? 

- DB서버가 구동되고 있는 환경에서 DB에 대해 명령문을 작업하게 되는데 이 떄의 명령문을 쿼리라고 말한다.
- 간단히 말하자면 사용자가 질의(쿼리문)을 DB에 보내면 DB서버는 그 질의문을 토대로 결과를 도출해내어 사용자에게 보여주는 것입니다.

---
# Query 구문?
-  CREATE DATABASE
    - 데이터 베이스를 새로 생성할 떄 사용
    - CREATE DATABASE [데이터베이스 명]
- DROP
    - 데이터 베이스 자체를 삭제할 떄 사용
    - 테이블을 삭제 할때도 사용가능
    - DROP DATABASE [데이터베이스 명]
    - DROP TABLE [테이블 명]
- ALTER
    - 테이블 칼럼을 추가 및 삭제할때 사용
    - ALTER TABLE [테이블명] ADD [추가할 컬럼이름] 데이터형(데이터크기) 컬럼속성
    - ALTER TABLE [테이블명] DROP COLUMN [삭제할 컬럼이름]
- TRUNCATE
    - DELETE와 다르게 테이블에 있는 데이터를 한번에 제거
    - 테이블이 최초 생성되었을 당시의 Storage만 남김(CREATE TABLE 한 직후의 상태가 됨)
    - DROP과의 차이점은 DROP은 테이블을 완전히 날려버리지만 TRUNCATE는 테이블이 남기 때문에 재사용 가능
- DISTINCT
    - 데이터의 중복을 제거 할 때 사용
    - select절에 distinct 키워드만 명시하면 되므로 쿼리문이 복잡하지 않고 간결
    - 유니크한 값을 받고 싶을 때에는 SELECT DISTINCT 를 사용
---
## 페이징이란?

- DB안에 대량의 데이터를 한번에 조회하지 않고 작은 단위로 나누어 필요한 부분만을 가져오는 기법입니다.
- 한번에 많은 데이터를 조회하면 시스템 리소스를 많이 소모하게 되고 모든 데이터를 한번에 보여주면 사용자가 원하는 데이터를 찾기 어려우며 마지막으로 네트워크 사용량이 많아지므로 페이징을 통해 데이터를 나누어 읽어서 이를 해결합니다.
- 페이징 기법에는 기본 페이지네이션, 커서 기반 페이징, 무한 스크롤이 있습니다.

---

# offset based 페이징

- 우리가 자주 봤던 페이징으로 직접 페이지 번호를 찾아내어 이동하는 페이징입니다.
- 구문은 limit Y offset (X-1) * Y; 입니다.
- 단점은 offset이 엄청 크면 다 조회해야한다 라는 것입니다.
  - offset을 모두 조회하고 스캔해서 테이블을 만들고 쪼개서 보여주므로 offset이 클수록 성능이 안 좋아질 수 있다.
  - 데이터 정확성 문제 : 직 접 여러 개의 데이터를 넘어가서 가져온다는 느낌이기에 페이지가 뒤로 갈수록 넘어가는 데이터가 많아져 성능 상의 이슈가 있고 읽어 들이는 중 데이터 추가가 되면 읽는 순서가 꼬일 수 있다.

---

# Cursor based 페이징

- cusor paing의 경우 이름에서 유추 할 수 있듯이 커서로 무언가를 가르켜 페이징을 하는 방법입니다. 여기서 커서는? ***마지막으로 조회한 콘텐츠입니다.***
- 구문은 보통 WHERE 끝에 <를 붙여서 그 뒤에 마지막으로 조회된 콘텐츠를 집어 넣습니다.
- 여기서 커서는 고유의 값을 가지는 것을 두어야 결과가 중복 되지 않고 조회 될 수 있습니다.
- 따라서 기본속성 + PK키를 합친 문자열을 커서로 설정하여 조회하는 방식을 사용합니다.
  - CONCAT(LPAD(r.star,10,'0'), LPAD(r.review_id,10,'0'))
- 또는 AND나 OR사용
  - r.star < ? or (r.star = ? and r.review_id <?)

---

# 핵심 키워드

- DB Join이란?
  - 둘 이상의 테이블을 연결해서 데이터를 검색하는 방법으로 연결하려면 테이블들이 적어도 하나의 컬럼을 공유하고 있어야한다. 여기서 공유하고 있는 칼럼을 PK또는 FK값으로 사용합니다.
- Join 종류들
  - INNER JOIN : 내부조인 -> 교집합
  - LEFT/RRIGHT JOIN : 부분집합
  - OUTER JOIN : 외부조인 -> 합집합
- 트랜잭션이란?
  - DB의 상태를 변환시키기 위해서 수행하는 작업의 단위
  - SELECT - 찾기
  - INSERT - 추가
  - DELETE - 삭제
  - UPDATE - 수정
- Join on 과 where의 차이
   - ON은 join하기 전에 조건을 필터링해주고 WHERE는 join 후에 조건을 필터링해주는 것으로 JOIN해주고 추가로 where로 조건을 준다고 생각해도 될 것 같다
---
# 미션
#### 0주차 때 직접 설계한 데이터베이스를 토대로 아래의 화면에 대한 쿼리를 작성
 <img alt="erd0" src="https://github.com/mybookG/image/blob/main/erd0.png?raw=true" />

***테이블 만들기***
<div style="border:1px solid #000; padding:10px;white-space: pre;tab-size:20;"> 
create database UMC;
USE UMC;<br>

create table User(
	userId	BIGINT PRIMARY KEY,
    name	varchar(20),
    gender	enum('M','F'),
    dateBirth	date,
    address	varchar(20),
    created_at	datetime(6),
    updated_at	datetime(6),
    status	enum('Boss','User','Admin'),
    inactive_date	DATETIME,
    point BIGINT
);

create table Store(
	store_id	bigint primary key,
	boos_id BIGINT, 
    introduce VARCHAR(20),
    map VARCHAR(20)
);

create table Mission(
	missionId BIGINT primary key,
    mPoint INT,
    mission_check VARCHAR(20),
    store_id BIGINT,
    boss_id BIGINT,
    foreign key (store_id) references Store(store_id)
); 

create table review(
	reviewId BIGINT primary	key,
    review varchar(20),
    scope INT,
    image VARCHAR(20),
    userId BIGINT,
    store_id bigint,
    boss_id bigint,
    foreign key(userId) references User(userId),
    foreign key(store_id) references Store(store_id)
);

create table UserMission(
	userMissionId bigint primary key,
    userId bigint,
    status ENUM('ING','DONE','FAIL'),
    mission_check	varchar(10),
    start_at	datetime(6),
    endd_at		datetime(6),
    completed_at	datetime(6),
    reviewId	bigint,
    foreign key(userId) references User(userId),
    foreign key(reviewId) references review(reviewId)
);

create table alram(
	alramId bigint primary key,
    userId	bigint,
    title varchar(20),
    content varchar(20),
    inImage	varchar(20),
    foreign key(userId) references User(userId)
);</div>


---

# 학습 후기
- 백앤드에게는 DB를 그저 잘 만들면 되는 줄 알았는데 조회하는 것이 더 어렵고 잘 해야한다고 알게되었다.
- 쿼리문을 짜내는 것은 생각보다 매우매우 어렵고 그 구문을 잘 짜고 오류가 안나게 하는것은 더더더 어려웠다.
- 쿼리에 대한 추가적인 공부가 시급하다고 느꼈고 이 것은 확실히 짚고 넘어가야 될 것 같다.

