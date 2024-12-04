# subjectMVCProject

자바 (Eclipse)와 Oracle DB를 사용한 학과 수강신청 관리 시스템

## 🖥️ 프로젝트 소개

이 프로젝트는 학과, 학생, 과목, 수강신청 정보를 관리할 수 있는 시스템입니다. 학생들이 수강할 과목을 신청하고, 학과와 관련된 정보를 조회, 수정할 수 있는 기능을 제공합니다. 이 시스템은 학과, 학생, 과목에 대한 관리 및 수강신청 기능을 포함하고 있습니다.

## 🕰️ 개발 기간

- 2024.11.29일 - 2024.12.3일

### 🧑‍🤝‍🧑 개발자

- 최우빈 - 프로젝트 설계, 데이터베이스 구축, 기능 구현, 통합 관리

### ⚙️ 개발 환경

- `Java 17`
- **IDE**: Eclipse
- **Database**: Oracle SQL Developer
- **jdk**: 21.0.4

## 📌 주요 기능

#### 1. 학과 관리

- 학과 정보 등록, 수정, 삭제
- 학과 정보 조회 및 목록 표시
- 학과 정보 정렬 기능

#### 2. 학생 관리

- 학생 정보 등록, 수정, 삭제
- 학생 정보 조회 및 목록 표시
- 학생의 학과 정보 조회

#### 3. 과목 관리

- 과목 정보 등록, 수정, 삭제
- 과목 정보 조회 및 목록 표시
- 과목 정보 정렬 기능

#### 4. 수강 신청 관리

- 학생이 과목을 선택하여 수강 신청
- 수강 신청 내역 조회 및 취소
- 수강 신청 조인 목록 조회

#### 5. ERD

<img src="https://github.com/rhdfyd21/oracleTest/blob/dev/image/project.png" width = "800px" height = "450px">

---

## 🧩 데이터베이스 스키마

프로젝트에서 사용된 주요 테이블과 관계는 아래와 같습니다:

- **SUBJECT**: 학과 정보 관리
- **STUDENT**: 학생 정보 관리
- **LESSON**: 과목 정보 관리
- **TRAINEE**: 수강신청 관리

### 📜 DB 테이블 생성 SQL
```sql
-- 학과 (01-컴퓨터학과 / 02-교육학과 / 03-신문방송학과 / 04-인터넷비즈니스과 / 05-기술경영과)
create table subject( 
    no number,                 -- pk, seq
    num varchar2(2) not null,  -- 학과번호 01, 02, 03,04,05
    name varchar2(24) not null -- 학과이름
);
Alter table subject add constraint subject_no_pk primary key(no); 
Alter table subject add constraint subject_num_uk UNIQUE(num);

create sequence subject_seq
start with 1
increment by 1; 

insert into subject(no, num, name) values (subject_seq.nextval, 03, 보안학과);
select * from subject;
DELETE FROM SUBJECT WHERE NUM = '05';
UPDATE SUBJECT SET NAME ='보안학과' WHERE NUM = '03';
SELECT * FROM SUBJECT ORDER BY NUM;
COMMIT; 
```
```sql
-- 학생
create table student( 
    no number,                    --pk, seq
    num varchar2(8) not null,     --학번(년도학과번호)
    name varchar2(12) not null,   --이름
    id varchar2(12) not null,     --아이디
    passwd varchar2(12) not null, --패스워드
    s_num varchar2(2) not null,   --학과번호(fk)
    birthday varchar2(8) not null,--생년월일 
    phone varchar2(15) not null,  --전화번호
    address varchar2(80) not null,--주소
    email varchar2(40) not null,  --이메일
    sdate date default sysdate    --등록일
);
commit;
SELECT * FROM STUDENT WHERE no = 1;
UPDATE STUDENT SET  passwd = 1234, birthday = '20001111', address = '화성', email = 'wohyuck@adf.sd' WHERE NO = 1;
--학생 검색
SELECT NUM,NAME,EMAIL FROM STUDENT WHERE NAME = '홍길동';
Alter table student add constraint student_no_pk primary key(no); 
Alter table student add constraint student_id_uk UNIQUE(id);
Alter table student add constraint student_num_uk UNIQUE(num);
Alter table student add constraint student_subject_num_fk 
    FOREIGN key(s_num) References subject(num) on delete set null;
alter table student drop constraint student_subject_num_fk;

create sequence student_seq
start with 1
increment by 1; 

select * from student;

INSERT INTO student 
VALUES (
    student_seq.nextval,  -- 시퀀스를 이용한 자동 증가
    '20230101',           -- 학번
    '홍길동',             -- 이름
    'hong123',            -- 아이디
    'password',           -- 패스워드
    '03',                 -- 학과번호 (보안학과)
    '20000101',           -- 생년월일
    '010-1234-5678',      -- 전화번호
    '서울특별시 강남구',    -- 주소
    'hong123@example.com',-- 이메일
    sysdate               -- 현재 날짜
); 
UPDATE STUDENT SET PASSWD = 1234;
select COUNT(*) AS COUNT from student where id = 10;
-- 동일학과번호 총갯수
select LPAD(count(*)+1,4,'0') as total_count from student where s_num = '05'; 
select * from student;
-- SUBJECT STUDENT INNER JOIN
SELECT STU.NO, STU.NUM, STU.NAME, STU.ID,PASSWD,STU.S_NUM,SUB.NAME AS SUBJECT_NAME ,BIRTHDAY,PHONE,ADDRESS, EMAIL, SDATE
FROM STUDENT STU INNER JOIN SUBJECT SUB ON STU.S_NUM = SUB.NUM ; 


```
```sql
-- lesson 과목

create table lesson( 
    no number ,                 --pk seq
    abbre varchar2(2) not null, --과목요약
    name varchar2(20) not null  --과목이름
);
Alter table lesson add constraint lesson_no_pk primary key(no); 
Alter table lesson add constraint lesson_abbre_uk UNIQUE(abbre);

create sequence lesson_seq 
start with 1
increment by 1;

commit;
drop table LESSON;
--테스팅 
select * from lesson;
DELETE FROM LESSON WHERE NO = 10;
UPDATE LESSON SET ABBRE = '01', NAME = '컴퓨터구조론' WHERE NO = 10;
INSERT INTO LESSON VALUES(LESSON_SEQ.NEXTVAL, '10','컴퓨터구조론');
```
```sql
-- trainee 수강신청
drop table trainee;

create table trainee( 
    no number ,                     --pk seq
    s_num varchar2(8) not null,     --student.num(fk) 학생번호
    abbre varchar2(2) not null,     --lesson.abbre(fk) 과목요약
    section varchar2(20) not null,  --전공,부전공,교양
    regdate date default sysdate      --수강신청일
);
select T.no, T.section, T.regdate, S.num, S.name, L.abbre, L.name as abbreName from trainee T 
inner join student S on T.s_num = S.num
inner join lesson L on T.abbre = L.abbre  
order by T.no;

Alter table trainee add constraint trainee_no_pk primary key(no);
Alter table trainee add constraint trainee_student_num_fk 
    FOREIGN key(s_num) References student(num) on delete set null;
Alter table trainee add constraint trainee_lesson_abbre_fk 
    FOREIGN key(abbre) References lesson(abbre) on delete set null;
    
    --테스팅
    UPDATE trainee 
SET s_num = 'DEFAULT_SNUM', abbre = 'DEFAULT_ABBRE', section = 'DEFAULT_SECTION' 
WHERE no = 10;

INSERT INTO trainee 
VALUES (trainee_seq.NEXTVAL, 'DEFAULT_SNUM', 'DEFAULT_ABBRE', 'DEFAULT_SECTION', sysdate);
create sequence trainee_seq 
start with 1
increment by 1;

commit;
drop table LESSON;
--테스팅 
select * from lesson;
DELETE FROM LESSON WHERE NO = 10;
UPDATE LESSON SET ABBRE = '01', NAME = '컴퓨터구조론' WHERE NO = 10;
INSERT INTO LESSON VALUES(LESSON_SEQ.NEXTVAL, '10','컴퓨터구조론');
