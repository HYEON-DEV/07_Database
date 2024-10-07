
```sql
use `myschool`;

create table `mytable` (
    `id` int not null primary key auto_increment,
    `memo` varchar(255) not null,
    `reg_date` datetime not null
) engine=innodb default charset `utf8`;

desc `mytable`;


drop table mytable;

show tables;
```


```sql
create table members (
    `id` int(11) not null primary key auto_increment comment '일련번호',
    `user_id` varchar(30) not null comment '아이디',
    `user_pw` varchar(255) not null comment '비밀번호(암호화저장)',
    `user_name` varchar(30) not null comment '회원이름',
    `email` varchar(255) not null comment '이메일',
    `phone` varchar(20) not null comment '연락처',
    `birthday` date not null comment '생년월일',
    `gender` enum('M','F') not null comment '성별(M=남자,F=여자)',
    `postcode` char(5) not null comment '우편번호',
    `addr1` varchar(255) not null comment '검색된 주소',
    `addr2` varchar(255) not null comment '나머지 주소',
    `photo` blob null comment '프로필사진 정보',
    `is_out` enum('Y','N') not null comment '탈퇴여부(Y/N)',
    `is_admin` enum('Y','N') not null comment '관리자 여부(Y/N)',
    `login_date` datetime null comment '마지막 로그인 일시',
    `reg_date` datetime not null comment '등록일시',
    `edit_date` datetime not null comment '변경일시'
) engine=innodb default charset`utf8` comment='회원';
```


# 테이블 수정하기

### ex )
테이블 수정하기 
```sql
mysql> show tables;
+--------------------+
| Tables_in_myschool |
+--------------------+
| department         |
| members            |
| mytable            |
| professor          |
| student            |
+--------------------+
5 rows in set (0.00 sec)

mysql> alter table `mytable` rename `mytest`;
Query OK, 0 rows affected (0.02 sec)

mysql> show tables;
+--------------------+
| Tables_in_myschool |
+--------------------+
| department         |
| members            |
| mytest             |
| professor          |
| student            |
+--------------------+
5 rows in set (0.00 sec)
```


### ex )
datetime 형식의 edit_Date 칼럼을 not null 추가
```sql
alter table `mytest` add `edit_date2` datetime not null;
```


### ex )
after  옵셩 사용
```sql
alter table `mytest` add `writer` varchar(50) null after `id`;

mysql> desc mytest;
+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| id         | int          | NO   | PRI | NULL    | auto_increment |
| writer     | varchar(50)  | YES  |     | NULL    |                |
| memo       | varchar(255) | NO   |     | NULL    |                |
| reg_date   | datetime     | NO   |     | NULL    |                |
| edit_date  | datetime     | NO   |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+
```


### ex )
memo 컬럼 수정하기
컬럼명 -> comment  /  데이터타입 -> text  /  null 허용
```sql
alter table mytest change memo comment text null;
mysql> desc mytest;
+------------+-------------+------+-----+---------+----------------+
| Field      | Type        | Null | Key | Default | Extra          |
+------------+-------------+------+-----+---------+----------------+
| id         | int         | NO   | PRI | NULL    | auto_increment |
| writer     | varchar(50) | YES  |     | NULL    |                |
| comment    | text        | YES  |     | NULL    |                |
| reg_date   | datetime    | NO   |     | NULL    |                |
| edit_date  | datetime    | NO   |     | NULL    |                |
+------------+-------------+------+-----+---------+----------------+
```


### ex )
writer 컬럼 이름을 유지한 상태에서 데이터 타입만 변경하기
=> 컬럼이름과 null허용 여부는 기존과 동일하게 명시하고,
변경하려는 데이터 타입만 기존의 형태와 다르게 지정한다
```sql
alter table mytest change writer writer varchar(100) not null;
```


### ex )
`edit_date` 컬럼 삭제하기
```sql
alter table mytest drop edit_date;

Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc mytest;
+----------+--------------+------+-----+---------+----------------+
| Field    | Type         | Null | Key | Default | Extra          |
+----------+--------------+------+-----+---------+----------------+
| id       | int          | NO   | PRI | NULL    | auto_increment |
| writer   | varchar(100) | NO   |     | NULL    |                |
| comment  | text         | YES  |     | NULL    |                |
| reg_date | datetime     | NO   |     | NULL    |                |
+----------+--------------+------+-----+---------+----------------+
```


### ex )
id 컬럼의 기본키 속성 해제하기
=> 기본키는 auto_increment 속성 있을경우 해제할 수 없기 때문에 change 로 속성을 제거해야 한다
=> 제거한 후 id 컬럼에 기본키 설정하고 auto_increment 속성 추가하기
```sql
alter table mytest change id id int not null;

mysql> desc mytest;
+----------+--------------+------+-----+---------+-------+
| Field    | Type         | Null | Key | Default | Extra |
+----------+--------------+------+-----+---------+-------+
| id       | int          | NO   | PRI | NULL    |       |
| writer   | varchar(100) | NO   |     | NULL    |       |
| comment  | text         | YES  |     | NULL    |       |
| reg_date | datetime     | NO   |     | NULL    |       |
+----------+--------------+------+-----+---------+-------+


alter table mytest drop primary key;

mysql> desc mytest;
+----------+--------------+------+-----+---------+-------+
| Field    | Type         | Null | Key | Default | Extra |
+----------+--------------+------+-----+---------+-------+
| id       | int          | NO   |     | NULL    |       |
| writer   | varchar(100) | NO   |     | NULL    |       |
| comment  | text         | YES  |     | NULL    |       |
| reg_date | datetime     | NO   |     | NULL    |       |
+----------+--------------+------+-----+---------+-------+


alter table mytest add primary key (id);

alter table mytest change id id int not null auto_increment;
```