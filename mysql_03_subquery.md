
# subquery

- 서브쿼리를 포함한 SQL문을 '메인쿼리' 라고 한다
- 서브쿼리는 select문의 시작과 끝에 ()를 묶어서 메인쿼리와 구분한다
<br/> <br/> <br/>


## 단일행 서브쿼리


### ex )
'전은지' 교수와 같은 직급의 교수에 대한 이름, 직급 조회
```sql
select name, position from professor 
where position = (
select position from professor where name='전은지' );

+--------+----------+
| name   | position |
+--------+----------+
| 염일웅 | 전임강사 |
| 전은지 | 전임강사 |
+--------+----------+
```


### ex )  
- 집계 함수와 함께 사용하기

1학년 학생 중에서 키가 전체 학생의 평균키를 초과하는 학생의 이름, 학과번호, 학년, 키 조회
```sql
select name, deptno, grade, height from student
where grade=1 
and height > ( select avg(height) from student );

+--------+--------+-------+--------+
| name   | deptno | grade | height |
+--------+--------+-------+--------+
| 서재진 |    101 |     1 |    186 |
| 이동훈 |    201 |     1 |    172 |
| 박동진 |    201 |     1 |    182 |
| 조명훈 |    201 |     1 |    184 |
+--------+--------+-------+--------+
```


### ex )
- Join 함께 사용하기

이광훈 학생과 같은 학과에 재학중인 학생의 이름, 소속학과이름 조회
```sql
select name, dname from student s inner join department d on s.deptno = d.deptno
where s.deptno = ( select deptno from student where name = '이광훈' );

+--------+--------------+
| name   | dname        |
+--------+--------------+
| 전인하 | 컴퓨터공학과 |
| 박미경 | 컴퓨터공학과 |
| 김영균 | 컴퓨터공학과 |
| 지은경 | 컴퓨터공학과 |
| 임유진 | 컴퓨터공학과 |
| 서재진 | 컴퓨터공학과 |
| 이광훈 | 컴퓨터공학과 |
| 류민정 | 컴퓨터공학과 |
+--------+--------------+
```


## 다중행 서브쿼리
- 하나 이상의 검색결과를 반환하는 형태
- **IN** 연산자 사용


### ex )
급여를 300만원 초과로 받는 교수에게 지도받는 학생들의 학번, 학년, 이름 조회
```sql
select studno, grade, name from student 
where profno in ( select profno from professor where sal>300 );

select s.name, p.name from student s 
inner join professor p on s.profno = p.profno
where p.profno in ( select profno from professor where sal > 300 );

+--------+-------+--------+
| studno | grade | name   |
+--------+-------+--------+
|  20103 |     2 | 김진경 |
|  10101 |     4 | 전인하 |
|  10107 |     4 | 이광훈 |
|  10201 |     2 | 김진영 |
|  10202 |     4 | 오유석 |
|  10204 |     3 | 윤진욱 |
|  10103 |     3 | 김영균 |
+--------+-------+--------+
```


### 연습문제 1
아이디가 jun123인 학생과 같은 학년인 학생의 학번, 이름, 학년 조회
```sql
select studno, name, grade from student where grade = (
    select grade from student where userid='jun123'
);

+--------+--------+-------+
| studno | name   | grade |
+--------+--------+-------+
|  10101 | 전인하 |     4 |
|  10107 | 이광훈 |     4 |
|  10202 | 오유석 |     4 |
+--------+--------+-------+
```


### 연습문제 2
101번 학과 학생들의 평균 몸무게보다 몸무게가 적은 학생의 이름, 학과번호, 몸무게 조회
```sql
select name, deptno, weight from student where weight < (
    select avg(weight) from student where deptno = 101
);

+--------+--------+--------+
| name   | deptno | weight |
+--------+--------+--------+
| 박미경 |    101 |     52 |
| 지은경 |    101 |     42 |
| 임유진 |    101 |     54 |
| 김진영 |    102 |     48 |
| 이동훈 |    201 |     64 |
| 김진경 |    201 |     51 |
| 조명훈 |    201 |     62 |
+--------+--------+--------+
```


### 연습문제 3  **********
이광훈 학생과 같은 학과의 학생들에 대한 평균 몸무게보다 몸무게가 적게 나가는 학생들의 이름, 몸무게, 소속학과이름, 담당교수 이름 조회 (담당교수가 없는 학생은 출력되지 않는다)
```sql
select s.name, weight, dname, p.name as pname from student s 
inner join department d on s.deptno = d.deptno
right outer join professor p on s.profno = p.profno
where weight < ( select avg(weight) from student where deptno = (
    select deptno from student where name = '이광훈'
) );

select s.name, weight, dname, p.name as pname from student s 
inner join department d on s.deptno = d.deptno
inner join professor p on s.profno = p.profno
where s.weight < ( select avg(weight) from student where deptno = (
    select deptno from student where name = '이광훈'
) );


+--------+--------+----------------+--------+
| name   | weight | dname          | pname  |
+--------+--------+----------------+--------+
| 지은경 |     42 | 컴퓨터공학과   | 전은지 |
| 임유진 |     54 | 컴퓨터공학과   | 전은지 |
| 김진영 |     48 | 멀티미디어학과 | 권혁일 |
| 김진경 |     51 | 전자공학과     | 이재우 |
+--------+--------+----------------+--------+
```


### 연습문제 4
20101번 학생과 같은 학년이고, 20101번 학생의 키보다 큰 키를 갖는 학생의 이름, 학년, 키 조회
```sql
select name, grade, height from student 
where grade = ( select grade from student where studno = 20101 ) 
and height > ( select height from student where studno = 20101 );

+--------+-------+--------+
| name   | grade | height |
+--------+-------+--------+
| 서재진 |     1 |    186 |
| 박동진 |     1 |    182 |
| 조명훈 |     1 |    184 |
+--------+-------+--------+
```


### 연습문제 5
학과 이름에 '공학'이라는 단어를 포함하고 있는 학과에 재학중인 학생의 학번, 학과이름, 학년, 학생이름 조회
```sql
select studno, dname, grade, name from student s 
inner join department d on s.deptno = d.deptno
where s.deptno in ( select deptno from department where dname like '%공학%' );

+--------+--------------+-------+--------+
| studno | dname        | grade | name   |
+--------+--------------+-------+--------+
|  10101 | 컴퓨터공학과 |     4 | 전인하 |
|  10102 | 컴퓨터공학과 |     1 | 박미경 |
|  10103 | 컴퓨터공학과 |     3 | 김영균 |
|  10104 | 컴퓨터공학과 |     2 | 지은경 |
|  10105 | 컴퓨터공학과 |     2 | 임유진 |
|  10106 | 컴퓨터공학과 |     1 | 서재진 |
|  10107 | 컴퓨터공학과 |     4 | 이광훈 |
|  10108 | 컴퓨터공학과 |     2 | 류민정 |
|  20101 | 전자공학과   |     1 | 이동훈 |
|  20102 | 전자공학과   |     1 | 박동진 |
|  20103 | 전자공학과   |     2 | 김진경 |
|  20104 | 전자공학과   |     1 | 조명훈 |
+--------+--------------+-------+--------+
```