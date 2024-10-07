# 입력 INSERT

- 값을 나열할 때는 테이블 구조에서 정의하고 있는 컬럼의 순서에 맞게 명시해야 한다
- -> DESC 테이블이름;    테이블 정의 확인


### ex )
학생 테이블에 다음의 정보로 새로운 데이터 추가하기
```sql
insert into student values (
    10110,
    '홍길동',
    'hkd85',
    1,
    '8501011143098',
    '2013-10-01 11:42:30',
    '055)-777-7777',
    170,
    70,
    101,
    9903
);

Query OK, 1 row affected (0.03 sec)
```

<br/> <br/>

### < 컬럼명을 명시한 데이터 저장 >
- 나열되는 컬럼의 이름이 테이블 구조에서 정의하고 있는 컬럼순서와 일치할 필요 없다


### ex )
학생 테이블에 다음의 정보로 새로운 데이터를 컬럼명 나열하여 추가하기
```sql
insert into student (
    studno, name, userid, grade, idnum, birthdate, tel, height, weight, deptno, profno
) values (
    10111, '둘리', 'dolly', 2, '8202021234765', '2011-10-01 11:42:30', '02)-123-2345', 170, 70, 101, 9903
);
```

<br/> <br/>
- Null 항목에 yes 표시되는 컬럼은 null값 허용
- 암묵적인 방법 : 해당 컬럼의 이름과 값 생략
- 명시적인 방법 : 컬럼값에 NULL 사용
<br/>

### ex )
```sql
insert into department values (301, '영문학과', NULL);
```


### ex )
```sql
insert into professor (
    profno, name, userid, position, sal, hiredate, deptno
) values (
    9920, '고길동', 'gildong', '교수', 450, '2014-01-01', 102
);
```


<br/> <br/>
- 날짜 데이터
- 자동으로 현재 날짜 입력하기 위해서는 now() 함수 사용
- 따옴표 안쓴다
  

<br/> <br/>
- 중복되지 않기 위한 최소한의 제약 요구
- 테이블의 각 행을 고유하게 식별하는 값을 가진 열(또는 열 조합)을 기본키(Primary Key)라고 한다
- 테이블의 컬럼에 기본키(PK) 옵션을 지정해 중복 데이터 입력을 방지할 수 있다
- 기본키로 설정된 컬럼은 고유한 값만 저장해야 하기 때문에 다른 행과 중복된 값을 저장할 수 없도록 제약된다
- AUTO_INCREMENT 옵션 사용해 시스템에서 자동으로 일련번호 생성하도록 할 수 있다
- primary key 속성이 설정된 컬럼에 저장되는 값이 중복될 경우 에러 발생
- 기본키 제약조건
- 데이터가 중복지 않는다는 특성 : 데이터 무결성
- 무결성 제약조건

<br/>

### ex )
해당 컬럼 명시하지 않아도 자동으로 기존의 값들 중 가장 큰 값보다 1 큰값이 저장되어, 데이터 교유성을 유지하게 된다
```sql
insert into department (dname, loc) values ('국문학과', '5호관');
```

<br/>

## 데이터 수정

<br/>

### ex )
교수번호가 9903인 교수의 직급을 '조교수'로, 급여는 200만원으로 수정
```sql
select position, sal, name from professor where profno = 9903;
+----------+-----+--------+
| position | sal | name   |
+----------+-----+--------+
| 조교수   | 360 | 성연희 |
+----------+-----+--------+

update professor 
set position = '조교수', sal = 200  
where profno = 9903;

Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select position, sal, name from professor where profno = 9903;
+----------+-----+--------+
| position | sal | name   |
+----------+-----+--------+
| 조교수   | 200 | 성연희 |
+----------+-----+--------+
```

<br/>

## 데이터 삭제

<br/>

### ex )
학생번호가 20103번인 학생의 데이터 삭제
```sql
delete from student where studno = 20103;

Query OK, 1 row affected (0.01 sec)
```

<br/>

###  주의
- where절 사용하지 않는 update
- -> 모든 데이터가 같은 값으로 수정된다
- where절 사용하지 않는 delete 구문
- -> 모든 데이터가 삭제된다

<br/> <br/>


## 함수 사용

<br/> <br/>

### ex )
20101 학번 학생의 생년월일을 현재 시각으로 변경
```sql
update student set birthdate = now() where studno = 20101;
```

<br/>

### 연습문제 1
데이터 입력, 결과 조회
```sql
insert into department (deptno, dname,loc) values (103, '인터넷정보과', '공학관');

select deptno, dname, loc from department;

+--------+----------------+--------+
| deptno | dname          | loc    |
+--------+----------------+--------+
|    101 | 컴퓨터공학과   | 1호관  |
|    102 | 멀티미디어학과 | 2호관  |
|    103 | 인터넷정보과   | 공학관 |
|    201 | 전자공학과     | 3호관  |
|    202 | 기계공학과     | 4호관  |
|    301 | 영문학과       | NULL   |
|    302 | 국문학과       | 5호관  |
+--------+----------------+--------+
```


### 연습문제 2
공학관에 위치한 학과의 위치를 5호관으로 수정
```sql
update department set loc = '5호관' where loc = '공학관';

mysql> select deptno, dname, loc from department where loc='5호관';
+--------+--------------+-------+
| deptno | dname        | loc   |
+--------+--------------+-------+
|    103 | 인터넷정보과 | 5호관 |
|    302 | 국문학과     | 5호관 |
+--------+--------------+-------+
```

### 연습문제 3
학과번호 300번 이상인 학과 삭제
```sql
delete from department where deptno >= 300;

mysql> select deptno, dname, loc from department;
+--------+----------------+-------+
| deptno | dname          | loc   |
+--------+----------------+-------+
|    101 | 컴퓨터공학과   | 1호관 |
|    102 | 멀티미디어학과 | 2호관 |
|    103 | 인터넷정보과   | 5호관 |
|    201 | 전자공학과     | 3호관 |
|    202 | 기계공학과     | 4호관 |
+--------+----------------+-------+
```
