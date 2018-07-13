# SQL 문의 기본 - SELECT 문의 이해


* [SELECT 문의 기본](#select-문의-기본)
    * [테이블을 조작하는 SQL](#테이블을-조작하는-sql)
    * [WHERE 조건이 필요한 이유](#where-조건이-필요한-이유)
    * [연산자 우선순위](#연산자-우선순위)
    * [SQL의 기초적인 기술 규칙](#sql의-기초적인-기술-규칙)
    * [DISTINCT에 의한 중복 배제](#distinct에-의한-중복-배제)
* [SELECT 문의 응용](#select-문의-응용)
    * [검색결과 정렬](#검색결과-정렬)
    * [정렬 수행 시 주의할 점](#정렬-수행-시-주의할-점)
    * [테이블을 요약하는 함수](#테이블을-요약하는-함수)
    * [문자열을 집약하는 GROUP_CONCAT](#문자열을-집약하는-group_concat)
    * [DISTINCT로 중복 회피](#distinct로-중복-회피)
    * [데이터를 그룹으로 나누는 GROUP BY](#데이터를-그룹으로-나누는-group-by)
    * [나눈 그룹에 조건 추가](#나눈-그룹에-조건-추가)
    * [집약한 결과에 조건 지정](#집약한-결과에-조건-지정)
* [데이터의 갱신과 삽입](#데이터의-갱신과-삽입)
    * [데이터를 변경하는 UPDATE 문](#데이터를-변경하는-update-문)
    * [복수 열의 동시 갱신](#복수-열의-동시-갱신)
    * [데이터를 입력하는 INSERT문](#데이터를-입력하는-insert문)
    * [INSERT 문의 기본 구문](#insert-문의-기본-구문)
    * [데이터 입력에 자주 사용되는 구문](#데이터-입력에-자주-사용되는-구문)
    * [데이터를 제거하는 DELETE 문](#데이터를-제거하는-delete-문)
* [뷰의 작성과 서브쿼리 및 결합](#뷰의-작성과-서브쿼리-및-결합)
    * [뷰를 사용하는 이점](#뷰를-사용하는-이점)
    * [뷰를 작성하는 CREATE VIEW 문](#뷰를-작성하는-create-view-문)
    * [뷰로의 입력, 갱신의 제한](#뷰로의-입력-갱신의-제한)
    * [서브쿼리의 실행이란](#서브쿼리의-실행이란)
    * [결합이란](#결합이란)
    * [내부결합이란](#내부결합이란)
    * [외부결합이란](#외부결합이란)
   
&nbsp;
## SELECT 문의 기본 
### 테이블을 조작하는 SQL
SQL은 주로 DBMS에 저장된 테이블을 조작하기 위해 사용한다. 테이블은 스키마(폴더)에 저장되어 있다. MySQL에서는 스키마와 데이터베이스가 같으므로 데이터베이스라고 표기한다. 폴더처럼 데이터베이스도 여러 개를 작성할 수 있어서 테이블을 보려면 데이터베이스를 선택해야 한다.


1개의 데이터베이스에는 복수의 테이블이 저장되어 있다. MySQL의 경우 데이터베이스를 살펴보려면 'show databases;', 디폴트(현재) 데이터베이스를 지정하려면 'use 데이터베이스명;', 데이터베이스 내의 테이블 리스트를 얻을 때는 'show tables;' 명령을 사용한다. 여기서 show나 use는 SQL이 아니고 관리 명령이다.


SELECT 문의 기본은 '어디서(FROM 테이블명) 무엇을 가지고 온다(SELECT 열명)'고 적는다.
```
SELECT 열명 FROM 테이블명;
```


열명에 별표(*)를 사용하면 'FROM 테이블명'에서 지정한 테이블의 전체 열을 지정할 수 있고, 임의의 열을 콤마(,)로 구분해 복수로 지정하는 것도 가능하다. 테이블명은 표 자체의 이름을 사용하거나 암묵적으로 디폴트 데이터베이스의 테이블을 지정하는 것도 가능하며, 명시적으로 '데이터베이스명.테이블명' 형태로도 지정할 수 있다.


동일한 폴더에 동일한 이름의 파일을 작성하는 것은 불가능하지만, 다른 폴더에는 동일한 이름의 파일을 작성할 수 있다. 데이터베이스가 다르면 같은 이름의 테이블을 작성할 수 있다. 이 경우에 테이블명뿐이라면 어떤 데이터베이스의 테이블을 가리키는지 알기 어려운 경우가 있다.


**명령** | **설명**
----- | -----
select * from city; | 디폴트 데이터베이스의 city를 SELECT한다.
select * from world.city; | '데이터베이스명.테이블명' 형태로 명시적으로 world 데이터베이스의 city를 SELECT한다.


### WHERE 조건이 필요한 이유
데이터베이스의 테이블에서 열의 수는 테이블을 작성(정의)했을 때 결정되어 이후 명시적으로 변경하지 않는 한 늘어나지 않는다. 하지만 행수는 제한 없이 늘릴 수 있다. 따라서 필요한 데이터를 효율성 있게 클라이언트로 가져오기 위해서도 SELECT하는 행수를 줄여야 한다. 이럴 경우에는 WHERE 구문을 지정해 'WHERE 조건'의 형태로 SELECT 문의 끝에 추가한다.
```
SELECT 열명 FROM 테이블명 WHERE 조건;
```


조건은 테이블의 각 행을 살펴보고 일치하는 행만 SELECT한다.


비교 연산자:
**연산자 종류** | **의미**
----- | -----
= | 같다
< > | 같지 않다
> = | 같거나 크다
> | 보다 크다
< = | 같거나 작다
< | 보다 작다


예를 들어, 지역을 나타내는 district 열에서 전라남도를 나타내는 'chollanam'이라는 값과 동일한 행을 SELECT할 때 다음과 같은 SQL을 사용한다.
```
SELECT * FROM city WHERE district = 'chollanam';

+------+------------+-------------+-----------+------------+
| ID   | Name       | CountryCode | District  | Population |
+------+------------+-------------+-----------+------------+
| 2360 | Sunchon    | KOR         | Chollanam |     249263 |
| 2361 | Mokpo      | KOR         | Chollanam |     247452 |
| 2370 | Yosu       | KOR         | Chollanam |     183596 |
| 2390 | Kwang-yang | KOR         | Chollanam |     122052 |
| 2397 | Naju       | KOR         | Chollanam |     107831 |
+------+------------+-------------+-----------+------------+
```


또한, 인구(population)가 15만 명보다 큰 도시만을 SELECT할 떄는 다음과 같은 SQL을 사용한다.
```
SELECT Name, Population FROM city WHERE district = 'chollanam' AND population > 150000;

+---------+------------+
| Name    | Population |
+---------+------------+
| Sunchon |     249263 |
| Mokpo   |     247452 |
| Yosu    |     183596 |
+---------+------------+
```


거꾸로 어떠한 조건이라도 만족하면 되는 경우에만 AND 연산자 대신에 OR 연산자를 사용한다.


### 연산자 우선순위
위에서부터 우선순위가 높고 아래로 갈수록 우선순위가 낮다. 같은 행의 연산자는 우선순위가 같다.
**연산자** |
----- |
INTERVAL
BINARY, COLLATE
-(단항 감산), ~(단항 비트 반전)
^
*, /, DIV, %, MOD
-, +
<<, >>
&
|
=(비교 등호), <=>, >=, >, <=, <, <>, !=, IS, LIKE, REGEXP, IN
BETWEEN, CASE, WHEN, THEN, ELSE
NOT
&&, AND
XOR
||, OR
=(대입 등호), :=


조건이 복잡한 경우 생각하지 않았던 순으로 평가된다면 의도치 않은 결과를 얻기도 한다. 이를 피하려면 괄호를 사용해 우선순위를 명확히 해야 한다.


### SQL의 기초적인 기술 규칙
SQL은 그래픽 인터페이스(GUI)를 사용하고 마우스로도 조작할 수 있지만, 기본은 문자를 기반으로 기술하는 것이다. SQL을 기술할 때에는 다음과 같은 규칙이 있는데, 여기에 맞추지 않으면 오류가 되므로 주의해야 한다.
* SQL 문의 마지막에 딜리미터(문장의 단락을 의미하는 기호)를 붙인다(대부분은 세미클론).
* 키워드(예를 들어, SELECT나 FROM 등)의 대문자와 소문자는 구별하지 않는다. 즉, select도 SELECT도 Select도 같은 의미다.
* 정수는 기존 그대로 쓴다. 문자열이나 날짜 시각은 작은따옴표('')로 감싼다. 즉, 수치로 값을 지정하는 경우는 100000이지만, 문자열로 지정하는 경우는 '100000'이 된다.
* 단어(word)는 반각 스페이스나 개행으로 구별한다. 전각 스페이스는 사용하지 않는다.


### DISTINCT에 의한 중복 배제
선택한 행에서 중복된 값이 있고 이를 없애고 싶은 경우에는 'DISTINCT' 키워드를 지정한다.


다음 예는 한국(countrycode = 'KOR')으로 SELECT해서 얻어진 중복된 행정구역(district: 지역)에서 '중복 행'을 제외해서 구하는 SQL이다. 중복을 전부 제거한, '행정구역명 만'을 추출한다.
```
select distinct district from city where countrycode = 'kor';
해당 쿼리와 동일한 결과를 나타내는 쿼리는 다음과 같다:
select district from city where countrycode = 'kor' group by district;

+---------------+
| district      |
+---------------+
| Seoul         |
| Pusan         |
| Inchon        |
| Taegu         |
| Taejon        |
| Kwangju       |
| Kyongsangnam  |
| Kyonggi       |
| Chollabuk     |
| Chungchongbuk |
| Kyongsangbuk  |
| Chungchongnam |
| Cheju         |
| Chollanam     |
| Kang-won      |
+---------------+

```


![기본적인 SELECT 문](https://github.com/0jun0815/YJStudy/blob/master/데이터베이스/SQL%20문의%20기본%20-%20SELECT%20문의%20이해/images/기본적인%20SELECT%20문.jpg)


&nbsp;
## SELECT 문의 응용
### 검색결과 정렬
보통 SELECT 문을 실행하면 그 결과는 '열(컬럼)'과 '행(레코드)'으로 구성된 2차원 표로 표시된다. 열의 순서는 'SELECT 열1, 열2, ...'와 같이 명시적으로 지정하지 않는 한은 표를 작성할 때의 순서이다. 행의 순서는 'ORDER BY'란 명령으로 지정해야 한다.
```
SELECT ~ ORDER BY 열1[, 열2, ...]
```


ORDER BY에 사용한 열명을 '정렬키'라고 한다. 기본적으로 '오름차순'으로 정렬되며 '내림차순'으로 정렬하려면 열을 지정한 후에 'DESC'라는 키워드를 추가한다.
```
select * from city where countrycode = 'KOR' order by population desc;

+------+------------+-------------+---------------+------------+
| ID   | Name       | CountryCode | District      | Population |
+------+------------+-------------+---------------+------------+
| 2331 | Seoul      | KOR         | Seoul         |    9981619 |
| 2332 | Pusan      | KOR         | Pusan         |    3804522 |
| 2333 | Inchon     | KOR         | Inchon        |    2559424 |
|                              ...                             |
+------+------------+-------------+---------------+------------+
```


'ASC'라는 키워드를 추가해도 오름차순 정렬이 가능하다.


### 정렬 수행 시 주의할 점
ORDER BY에 의한 정렬을 수행할 때 행의 순서를 확실히 같게 하려면 행의 정렬키를 한 가지 의미(Unique)로 정해야 한다. 정렬키가 같은 값의 행이 복수 개 존재한다면 그 행들의 순서는 일정하지 않다. 다음처럼 행정구역(district)을 정렬키로 정렬하는 경우 전라남도는 5개 행이 있는데 그 5개 행 내의 순서는 일정하지 않게 된다.
```
select * from city where countrycode = 'kor' and district = 'chollanam' order by district;

+------+------------+-------------+-----------+------------+
| ID   | Name       | CountryCode | District  | Population |
+------+------------+-------------+-----------+------------+
| 2360 | Sunchon    | KOR         | Chollanam |     249263 |
| 2361 | Mokpo      | KOR         | Chollanam |     247452 |
| 2370 | Yosu       | KOR         | Chollanam |     183596 |
| 2390 | Kwang-yang | KOR         | Chollanam |     122052 |
| 2397 | Naju       | KOR         | Chollanam |     107831 |
+------+------------+-------------+-----------+------------+
```


다음 6개 행을 일정한 순서로 나열하려면 name 열도 콤마(,)로 구분해 정렬키로 지정해야 한다.
```
select * from city where countrycode = 'kor' and district = 'chollanam' order by district, name;

+------+------------+-------------+-----------+------------+
| ID   | Name       | CountryCode | District  | Population |
+------+------------+-------------+-----------+------------+
| 2390 | Kwang-yang | KOR         | Chollanam |     122052 |
| 2361 | Mokpo      | KOR         | Chollanam |     247452 |
| 2397 | Naju       | KOR         | Chollanam |     107831 |
| 2360 | Sunchon    | KOR         | Chollanam |     249263 |
| 2370 | Yosu       | KOR         | Chollanam |     183596 |
+------+------------+-------------+-----------+------------+
```


### 테이블을 요약하는 함수
SQL에서 데이터에 대해 어떠한 조작이나 계산을 수행하려면 '함수'라는 도구를 사용한다.


함수는 크게 나누어 다음 2종류가 있다.
* 복수 행(이나 행의 값)에 대해 집계를 수행하는 함수
* 단일 행의 값에 대해 조작이나 계산을 수행하는 함수


집계용 함수를 '집약함수(집계함수)'로 부르며 대표적으로 다음 5가지가 있다.
* COUNT: 테이블의 행수를 알려주는 함수
* SUM: 테이블의 수치 데이터를 합계하는 함수
* AVG: 테이블의 수치 데이터 평균을 구하는 함수
* MAX: 테이블의 임의열 데이터 중 최대값을 구하는 함수
* MIN: 테이블의 임의열 데이터 중 최소값을 구하는 함수


집약함수는 기본적으로 NULL을 제외하고 집계하는데, COUNT 함수만은 'COUNT(*)'로 표기하여 NULL을 포함한 전체 행을 집계한다. SUM, AVG 이외의 집약함수는 수치 데이터 외에도 이용할 수 있다. 다만 문자를 표현하는 내부 코드에 의존하므로 이용할 수 있는 예는 한정된다.


예를 들어, 도시(city)명(name) 최대값(max)은 'Y'로 시작하는 'Yosu', 최소값(min)은 'A'로 시작하는 'Andong'이 된다.
```
select max(name) from city where countrycode = 'kor';

+-----------+
| max(name) |
+-----------+
| Yosu      |
+-----------+
```
```
select min(name) from city where countrycode = 'kor';

+-----------+
| min(name) |
+-----------+
| Andong    |
+-----------+
```


### 문자열을 집약하는 GROUP_CONCAT
문자열에 대해 집약함수는 SQL 표준에는 없으나 MySQL에는 'GROUP_CONCAT' 함수(LIST 함수)가 있다. GROUP_CONCAT 함수는 '문자열'에 대한 집계를 '문자열의 결합'(CONCATinate)으로 수행한다. 따라서 콤마로 구분되는 매우 긴 데이터를 결과로 돌려준다.


GROUP_CONCAT 함수에서는 쉽게 예상외의 길이를 가진 결과를 돌려주는 경우가 있다. 이 때문에 SQL을 실행하는 애플리케이션을 작성하는 경우에 애플리케이션 측에서 GROUP_CONCAT 함수의 결과를 저장하거나 표시하는 경우에는 주의가 필요하다.


### DISTINCT로 중복 회피
DISTINCT 키워드는 집약함수로도 이용할 수 있다. 예를 들어, 행정구역(district)을 단순하게 GROUP_CONCAT한다면 경상남도(kyongsangnam)는 결과 값에서 여러 번 나오게 된다.
```
select group_concat(district) from city where countrycode = 'kor';

| Seoul,Pusan,Inchon,Taegu,Taejon,Kwangju,Kyongsangnam,Kyonggi,Kyonggi,Kyonggi,Kyonggi,Chollabuk,Chungchongbuk,Kyonggi,Kyonggi,Kyongsangbuk,Kyongsangnam,Kyongsangnam,Kyonggi,Chungchongnam,Kyongsangnam,Chollabuk,Kyonggi,Kyongsangbuk,Kyonggi,Kyongsangbuk,Chollabuk,Cheju,Kyongsangnam,Chollanam,Chollanam,Kyonggi,Kang-won,Kyonggi,Kang-won,Kyonggi,Kang-won,Chungchongbuk,Kyongsangbuk,Chollanam,Kyongsangbuk,Kyonggi,Kyongsangnam,Kyonggi,Chungchongnam,Kyongsangnam,Kyongsangbuk,Chungchongnam,Kyonggi,Chollabuk,Chungchongbuk,Chungchongnam,Kyonggi,Kyongsangnam,Chungchongnam,Kyongsangbuk,Kyongsangnam,Kyongsangbuk,Chungchongnam,Chollanam,Kyongsangnam,Kyonggi,Chollabuk,Kyongsangbuk,Kyongsangnam,Kyonggi,Chollanam,Chollabuk,Kang-won,Kyongsangbuk |
```


'DISTINCT district'와 같이 DISTINCT 키워드를 추가하면 중복이 없어지고 각 행정구역은 1회만 나타난다.
```
select group_concat(DISTINCT district) from city where countrycode = 'kor';

| Cheju,Chollabuk,Chollanam,Chungchongbuk,Chungchongnam,Inchon,Kang-won,Kwangju,Kyonggi,Kyongsangbuk,Kyongsangnam,Pusan,Seoul,Taegu,Taejon |
```


DISTINCT는 집약함수의 괄호 안에 작성한다.


### 데이터를 그룹으로 나누는 GROUP BY
집약함수는 대상이 되는 데이터를 몇 개의 그룹으로 나눠서 집약하는 것도 가능하다. 그룹을 나눌 때는 나누는 키가 되는 열을 지정한다. 


예를 들어, '행정구역(district)'마다, '국가(country)'마다 집약하는 식이다. 이런 그룹 분리에 이용하는 것이 'GROUP BY'이다.
```
SELECT ~ FROM 테이블명 GROUP BY 열명1 [, 열명2, ...]
```


GROUP BY로 지정한 열을 '집약 키'나 '그룹화 키'로 부르며, 이들은 ORDER BY처럼 복수 열을 콤마로 구분해 지정할 수 있다. 


다음 2가지 쿼리의 결과를 비교하면 GROUP BY가 없을 때는 대상 데이터 전체로 집약해 1건의 결과를 되돌려 주지만, GROUP BY가 있을 때는 지정한 그룹 열마다 집약해서 그룹 열로 나눈 그룹 수의 결과를 되돌려준다는 것을 알 수 있다.


GROUP BY 없음 결과:
```
select count(*) from city where countrycode = 'kor';

+----------+
| count(*) |
+----------+
|       70 |
+----------+
```


GROUP BY 있음 결과:
```
select district, count(*) from city where countrycode = 'kor' group by district;

+---------------+----------+
| district      | count(*) |
+---------------+----------+
| Seoul         |        1 |
| Pusan         |        1 |
| Inchon        |        1 |
| Taegu         |        1 |
| Taejon        |        1 |
| Kwangju       |        1 |
| Kyongsangnam  |       11 |
| Kyonggi       |       18 |
| Chollabuk     |        6 |
| Chungchongbuk |        3 |
| Kyongsangbuk  |       10 |
| Chungchongnam |        6 |
| Cheju         |        1 |
| Chollanam     |        5 |
| Kang-won      |        4 |
+---------------+----------+
```


### 나눈 그룹에 조건 추가
그룹으로 나눠서 집약한 값(count(*))에 대해 조건을 설정하려면 어떻게 할까? '조건'이 있다면 'WHERE' 구문에 조건을 추가(AND 연산자 사용)하고 싶을 것이다. 하지만 이 쿼리는 오류가 발생한다.
```
select district, count(*) from city where countrycode = 'kor' and count(*) = 4 group by district;

ERROR 1111 (HY000): Invalid use of group function

```


COUNT 같은 집약함수를 작성할 수 있는 경우는 SELECT와 ORDER BY, 뒤에서 설명할 HAVING뿐이다.


### 집약한 결과에 조건 지정
그룹마다 집약한 값을 조건으로 선택하고 싶다면 HAVING 뒤에 조건을 다음과 같이 지정한다.
```
SELECT ~ FROM ~ GROUP BY ~ HAVING 그룹의 값에 대한 조건
```


예를 들어, 충청남도처럼 도시 수가 6개인 지역을 선택하고 싶다면 HAVING 뒤에 조건 'count(*) = 6'을 지정한다.
```
select district, count(*) from city where countrycode = 'kor' group by district having count(*) = 6;

+---------------+----------+
| district      | count(*) |
+---------------+----------+
| Chollabuk     |        6 |
| Chungchongnam |        6 |
+---------------+----------+
```


SELECT와 ORDER BY에도 집약함수를 기술할 수 있는데, ORDER BY로 기술하는 경우 ORDER BY 뒤에 기술한다.


예를 들어, 도시 수가 5보다 큰(count(*) > 6) 그룹을 도시 수가 많은 순으로 정렬(ORDER BY count(*) DESC)하려면 다음과 같은 쿼리를 실행한다.
```
select district, count(*) from city where countrycode = 'kor' group by district having count(*) > 6 order by count(*) desc;

+--------------+----------+
| district     | count(*) |
+--------------+----------+
| Kyonggi      |       18 |
| Kyongsangnam |       11 |
| Kyongsangbuk |       10 |
+--------------+----------+
```


'ORDER BY, GROUP BY, HAVING'의 작성 순서는 다음과 같다. 작성할 때에는 반드시 이 순서대로 해야 한다.
*  1 SELECT / 2 FROM / 3 WHERE / 4 GROUB BY / 5 HAVING / 6 ORDER BY


![SELECT 문의 응용](https://github.com/0jun0815/YJStudy/blob/master/데이터베이스/SQL%20문의%20기본%20-%20SELECT%20문의%20이해/images/SELECT%20문의%20응용.jpg)


&nbsp;
## 데이터의 갱신과 삽입
### 데이터를 변경하는 UPDATE 문
기존 데이터를 변경(갱신)할 때는 'UPDATE 문'을 사용한다.
```
UPDATE 테이블명 SET 열명 = 값
```


이 구문에서 'SET'으로 지정한 열 이외의 것은 변경되지 않는다. 또한, 지정한 테이블 전체 행에대해 열에 동일한 값을 적용할 때는 이 구문을 잘 사용하지 않는다. 보통은 갱신할 대상을 WHERE로 간추리는 다음 구문이 자주 사용된다.
```
UPDATE 테이블명 SET 열명 = 값 WHERE 조건;
```


WHERE로 지정한 조건은 각 행에 대한 것이고 갱신은 WHERE 구문에 부합하는 행 전부에 대해 수행되므로 1행을 갱신하려면 WHERE에서 대상 행을 유니크하게 지정해야 한다. 행을 유니크하게 지정하는 데는 복수 열의 값을 AND로 지정하거나 유니크한 것이 분명한 열을 조건으로 지정한다.


### 복수 열의 동시 갱신
갱신 열별로 구분해 지정(SET 열명1 = 값1[열명2 = 값2, ...])하면 복수 개의 열을 동시에 갱신할 수 있다.
```
update city set name = 'Siheung', population = 429390 where countrycode = 'kor' and district = 'kyonggi' and name = 'Shihung';
```


또한, 갱신하는 열에 디폴트 값(기본값)이 있는 경우에는 값 대신에 'DEFAULT' 키워드를 지정하면 기본값으로 갱신할 수 있다. 열에 기본값이 있는지 없는지 확인하려면 테이블의 정의를 보면 된다.


### 데이터를 입력하는 INSERT문
테이블에 데이터를 입력할 때는 'INSERT' 문을 사용한다. INSERT는 행 단위로 수행되므로 행을 구성하는 열의 정의 정보를 포함한 '테이블 정의'를 정확하게 알아야 한다. MySQL에서는 다음 명령으로 테이블 정의를 알 수 있다('\G'는 ';' 대신에 사용할 수 있는 딜리미터로 결과를 세로로 보기 쉽게 한다).
```
show create table 테이블명/G
```


city 테이블 정의는 다음과 같다.
```
show create table city\G

*************************** 1. row ***************************
Table: city
Create Table: CREATE TABLE `city` (
`ID` int(11) NOT NULL AUTO_INCREMENT,
`Name` char(35) NOT NULL DEFAULT '',
`CountryCode` char(3) NOT NULL DEFAULT '',
`District` char(20) NOT NULL DEFAULT '',
`Population` int(11) NOT NULL DEFAULT '0',
PRIMARY KEY (`ID`),
KEY `CountryCode` (`CountryCode`),
CONSTRAINT `city_ibfk_1` FOREIGN KEY (`CountryCode`) REFERENCES `country` (`code`)
) ENGINE=InnoDB AUTO_INCREMENT=4081 DEFAULT CHARSET=latin1
```


또는 테이블 정의 자체가 아닌 단순히 열의 정보를 알고 싶은 경우에는 Oracle과 호환되는 명령인 'desc 테이블명'으로도 확인할 수 있다.
```
desc city;

+-------------+----------+------+-----+---------+----------------+
| Field       | Type     | Null | Key | Default | Extra          |
+-------------+----------+------+-----+---------+----------------+
| ID          | int(11)  | NO   | PRI | NULL    | auto_increment |
| Name        | char(35) | NO   |     |         |                |
| CountryCode | char(3)  | NO   | MUL |         |                |
| District    | char(20) | NO   |     |         |                |
| Population  | int(11)  | NO   |     | 0       |                |
+-------------+----------+------+-----+---------+----------------+
```


앞의 정보로 다음 내용을 알 수 있다.
1. 열수는 5(ID, Name, CountryCode, District, Population)
2. ID 열과 Population 열의 데이터형은 'INT' 형, 기타열은 'CHAR' 형
3. ID 열은 기본키로 설정되어 있으며 auto_increment 속성이 붙어 있다.


2의 'INT' 형은 'INTEGER(정수)'의 약어이다. 정수를 넣는 열에 지정하는 데이터형(수치형)으로, 소수점을 포함하는 실수는 넣을 수 없다. '(11)'과 같은 (n) 표기가 있는데 이것은 MySQL 특유의 '화면 표시용 폭'을 나타내는 것으로 CHAR 형처럼 열의 크기를 나타내는 것은 아니다.


'CHAR' 형은 'CHARCTER(문자)'의 약어로, 문자열을 넣는 열로 지정하는 데이터형(문자열형)이다. 'char(35)'나 'char(3)'처럼 열 속에 넣을 수 있는 문자열의 길이(최대 길이)를 괄호로 지정한다. 최대 길이를 넘어서는 문자열은 저장할 수 없다.


기본키(Primary Key)는 테이블의 행을 유일하게 특정할 수 있는 유니크한 값을 저장하는 열이며 테이블에 한 개밖에 정의할 수 없다. 기본키를 설정한 열은 유니크함이 보장되므로 UPDATE나 DELETE로 대상 행을 지정해서 갱신하는 데 편리하다. 데이터 자체에 데이터를 특정하는 유니크한 정보가 있다면 이를 기본키로 하면 좋지만, 그런 정보가 없이 기계적으로 유니크한 번호를 할당하고 싶은 경우에는 'auto_increment' 속성을 사용할 수 있다. 이 속성은 입력된 행에 대해 자동으로 유니크한 번호를 추가한다.


### INSERT 문의 기본 구문
INSERT 문의 기본 구문은 다음과 같다.
```
INSERT INTO 테이블명(열1[, 열2, ...]) VALUES(값1[, 값2, ...]);
```


테이블명 뒤의 열 리스트와 VALUES 뒤의 값 리스트는 수와 데이터형이 일치되어야 한다. 또한, 테이블에 정의된 모든 열에 대해 VALUES에서 값을 설정하면 테이블명의 열 리스트를 생략할 수 있다(이 경우 VALUES 뒤의 값 리스트는 테이블의 열 정의순으로 나열해야 한다).
```
insert into city values(DEFAULT, 'Gimpo', 'KOR', 'Kyonggi', 349900);
```


앞의 INSERT 문에서 첫 번째 열은 '값'이 아니라 'DEFAULT' 키워드를 지정했다. 테이블 정의에 기본값을 설정하는 경우 'INSERT 시 해당 기본값을 사용한다'라고 지정한다.


기본값으로 행을 입력하는 방법에는 여기서 다룬 명시적으로 DEFAULT를 값으로써 지정하는 방법이나 열 리스트로부터 빼서 암묵적으로 지정하는 방법이 있다. 예를 들면, 다음과 같이 열 리스트에서 첫 번째 열의 ID를 빼고 값 리스트에서도 ID로 지정한 값을 빼는 경우 ID에는 기본값이 적용되어 그 결과 실습에서 실행한 INSERT 문과 같은 데이터가 입력된다.
```
insert into city(name, countrycode, district, population) values('Gimpo', 'KOR', 'Kyonggi', 349900);
```


또한, city 테이블에서는 Countrycode 열을 제외한 4개의 열 전체에 기본값을 설정하고 있어서 전부 DEFAULT 값의 행을 작성할 수 있다. 그리고 암묵적으로 기본값을 설정할 때 최소한 한 개의 열 리스트를 지정해야 하기 때문에 Countrycode 열을 입력하는 다음과 같은 INSERT 문을 실행한다.
```
insert into city(Countrycode) values('KOR');
```


추가한 내용을 확인해보면 전체가 기본값(ID는 유니크한 일련번호, 문자열은 빈 문자, 숫자형은 0)인 행이 입력되어 있다.
```
select * from city order by id desc limit 3;

+------+-------+-------------+----------+------------+
| ID   | Name  | CountryCode | District | Population |
+------+-------+-------------+----------+------------+
| 4082 |       | KOR         |          |          0 |
| 4081 | Gimpo | KOR         | Kyonggi  |     349900 |
| 4079 | Rafah | PSE         | Rafah    |      92020 |
+------+-------+-------------+----------+------------+
```


### 데이터 입력에 자주 사용되는 구문
자주 사용되는 구문으로 'INSERT INTO 테이블1 SELECT * FROM 테이블2'가 있다. 이것은 VALUES 대신에 입력한 값(레코드)으로 SELECT 문의 결과를 사용하는 방법이다. 이 방법을 사용하면 기존의 데이터를 통해 1행으로 복수의 레코드 입력이 가능하다.


MySQL에서는 'CREATE TABLE 테이블명1 LIKE 테이블명2'란 특유의 구문으로 테이블명2와 같은 구조의 테이블(데이터는 없음)을 작성할 수 있으므로 이를 이용해 citycopy 테이블을 작성해서 city 테이블과 동일한 데이터를 INSERT 문으로 입력해보자.
```
create table citycopy like city;
insert into citycopy select * from city;
```


여기서 사용하는 SELECT 문은 SELECT 결과가 INSERT 대상 열의 열 정의와 같기 때문에 각종 구문을 추가해 실행할 수 있다. 예를 들어, WHERE 구문으로 조건을 추가하거나 LIMIT 구문으로 행수를 지정하는 것도 가능하다('LIMIT 3'을 하면 3행만을 돌려준다).


MySQL에서는 INSERT 문 하나로 신규 복수 행을 입력할 수 있는 '복수 행의 입력(Multi row insert)'이라는 기능이 있다. 예를 들어, 다음과 같은 복수의 레코드를 입력하려면 1행마다 INSERT 문을 넣어야 한다.
```
INSERT INTO city(name, countrycode, district, population) VALUES('Gimpo', 'KOR', 'Kyonggi', 359584);
INSERT INTO city(name, countrycode, district, population) VALUES('Pocheon', 'KOR', 'Kyonggi', 155192);
INSERT INTO city(name, countrycode, district, population) VALUES('Hwaseong', 'KOR', 'Kyonggi', 613091);
```


이것을 VALUES 뒤의 괄호를 콤마로 연결해 다음처럼 하나의 문장으로 작성할 수 있다.
```
INSERT INTO city(name, countrycode, district, population) VALUES('Gimpo', 'KOR', 'Kyonggi', 359584), ('Pocheon', 'KOR', 'Kyonggi', 155192), ('Hwaseong', 'KOR', 'Kyonggi', 613091);
```


### 데이터를 제거하는 DELETE 문
기존 데이터를 제거할 때 사용하는 DELETE 문의 기본 구문은 다음과 같다.
```
DELETE FROM 테이블명;
```


기본 구문 형태로 사용하면 지정한 테이블의 전체 행이 제거되므로 거의 사용하지 않고, 갱신하는 대상을 WHERE 구문으로 추려내는 다음 구문을 주로 사용한다.
```
DELETE FROM 테이블명 WHERE 조건;
```


WHERE로 지정한 조건은 각 행에 대한 것이다. 제거는 WHERE 구문과 일치한 행 전체에 대해 수행하므로 1행을 대상으로 제거하려면 WHERE 구문의 대상을 유니크하게 특정할 필요가 있다. 행을 유니크하게 특정하는 데는 복수의 열의 값을 AND로 지정하거나 유니크한 것이 분명한 열을 조건으로 지정한다.


![데이터의 갱신(UPDATE/INSERT/DELETE)](https://github.com/0jun0815/YJStudy/blob/master/데이터베이스/SQL%20문의%20기본%20-%20SELECT%20문의%20이해/images/데이터의%20갱신(UPDATE:INSERT:DELETE).jpg)


&nbsp;
## 뷰의 작성과 서브쿼리 및 결합
### 뷰를 사용하는 이점
뷰는 SQL 시점에서 보면 테이블과 동일하지만 테이블과 같은 데이터는 가지고 있지 않으며, 테이블에 대한 SELECT를 가지고 있다. 테이블 대신에 뷰를 사용하는 이점은 다음과 같다.
1. 복잡한 SELECT 문을 일일이 매번 기술할 필요가 없다.
2. 필요한 열과 행만 사용자에게 보여줄 수 있고, 갱신 시에도 뷰 정의에 따른 갱신으로 한정할 수 있다.
3. 1과 2의 이점을 데이터 저장 없이(기억장치의 용량을 사용하지 않고) 실현할 수 있다. 또한, 뷰를 제거(DROP VIEW)해도 참조하는 테이블은 영향을 받지 않는다.


### 뷰를 작성하는 CREATE VIEW 문
뷰를 작성할 때는 CREATE VIEW 문을 사용한다.
```
CREATE VIEW 뷰 명(열명1[, 열명2, ...]) AS SELECT 문;
```
```
create view citykyonggi as select id, name, population from city where countrycode = 'kor' and district = 'kyonggi';
```


원래의 SELECT 문에서 열 전체를 지정하는 경우에는 뷰명 뒤의 괄호와 열 리스트는 생략할 수 있다.


### 뷰로의 입력, 갱신의 제한
뷰로의 입력과 갱신에는 몇 가지 제한이 붙는다. 기본적으로는 어떤 행이 대응하는지 모르거나 어떤 값을 넣으면 좋을지 모르는 경우에는 갱신할 수 없다. 예를 들어, GROUP BY로 집약한 수치나 distinct로 얻은 값을 갱신하는 경우에는 결과의 기반이 되는 테이블의 n행 중 어떤 수치를 갱신하는 것이 좋은지를 알 수 없다. 또한, 2가지 이상의 테이블을 조합해 작성한 뷰를 갱신할 때는 어느 테이블을 갱신하면 좋을지를 알 수 없는 경우가 있다. 게다가 뷰에서 원래 테이블의 일부 열만 선택되었다면 데이터를 삽입하려고 해도 선택된 열 이외의 열에 기본값(DEFAULT 값)도 없고 NULL도 허용되지 않는(NOT NULL) 상황에서는 해당 열에 넣을 수 있는 값이 없어서 실질적으로 뷰로의 삽입은 불가능하다.


### 서브쿼리의 실행이란
통상적으로 SELECT 문의 결과는 택한 열과 행으로 구성된 테이블 형식이다. 그 특수한 형태로 하나의 열과 하나의 행으로 구성된 테이블, 즉 '단일값'으로 구성된 경우가 있다. SQL 문에서는 이런 SELECT 문의 결과를 마치 데이터처럼 다루거나 수치처럼 취급해 조건문에 이용할 수 있다. 이런 쿼리를 메인 쿼리와 대비해 '서브쿼리(Subquery)'라고 부른다.
```
select district, name, population from citykorea as c1 where population > (select avg(population) from citykorea as c2 where c1.district = c2.district group by district);

+---------------+-----------+------------+
| district      | name      | population |
+---------------+-----------+------------+
| Kyongsangnam  | Ulsan     |    1084891 |
| Kyonggi       | Songnam   |     869094 |
| Kyonggi       | Puchon    |     779412 |
| Kyonggi       | Suwon     |     755550 |
| Kyonggi       | Anyang    |     591106 |
| Chollabuk     | Chonju    |     563153 |
| Chungchongbuk | Chongju   |     531376 |
| Kyonggi       | Koyang    |     518282 |
| Kyonggi       | Ansan     |     510314 |
| Kyongsangbuk  | Pohang    |     508899 |
| Kyongsangnam  | Chang-won |     481694 |
| Kyongsangnam  | Masan     |     441242 |
| Chungchongnam | Chonan    |     330259 |
| Kyongsangnam  | Chinju    |     329886 |
| Chollabuk     | Iksan     |     322685 |
| Kyongsangbuk  | Kumi      |     311431 |
| Kyongsangbuk  | Kyongju   |     272968 |
| Chollabuk     | Kunsan    |     266569 |
| Chollanam     | Sunchon   |     249263 |
| Chollanam     | Mokpo     |     247452 |
| Kang-won      | Wonju     |     237460 |
| Kang-won      | Chunchon  |     234528 |
| Kang-won      | Kangnung  |     220403 |
| Chollanam     | Yosu      |     183596 |
+---------------+-----------+------------+
```

![서브쿼리의 이미지](https://github.com/0jun0815/YJStudy/blob/master/데이터베이스/SQL%20문의%20기본%20-%20SELECT%20문의%20이해/images/서브쿼리의%20이미지.jpg)


서브쿼리는 일반쿼리에서 테이블이나 단일값을 둔 장소 대부분에서 이요하는 것이 가능하며 다양한 변형이 있다.


### 결합이란
SQL은 2개 이상의 테이블을 대상으로 실행하는 것도 가능하다. 이때 자주 이용되는 것이 '결합(JOIN)'이다.


결합은 단순히 설명하면 하나의 테이블에 있는 열만으로는 데이터가 충족되지 않는 경우에 열을 가지고 오는 조작이다. 그냥 마구마구 가져오지는 않기 때문에 2개 테이블의 행을 결합시키기 위한 '결합조건'을 지정한다. '내부결합'과 '외부결합'이 있다.


### 내부결합이란
결합은 2개의 테이블에서 필요한 열을 가지고 올 때 행을 결합하기 위한 조건을 'ON'으로 지정한다. 내부결합(Inner Join)에서는 이 ON으로 지정한 결합 조건에 일치하는 행만을 2개의 테이블로부터 가져올 수 있다.


![내부결합](https://github.com/0jun0815/YJStudy/blob/master/데이터베이스/SQL%20문의%20기본%20-%20SELECT%20문의%20이해/images/내부결합.jpg)


```
select countrylanguage.*, country.name from countrylanguage INNER JOIN country ON countrylanguage.countrycode = country.code where language = 'Korean';

+-------------+----------+------------+------------+--------------------------+
| CountryCode | Language | IsOfficial | Percentage | name                     |
+-------------+----------+------------+------------+--------------------------+
| GUM         | Korean   | F          |        3.3 | Guam                     |
| JPN         | Korean   | F          |        0.5 | Japan                    |
| KOR         | Korean   | T          |       99.9 | South Korea              |
| MNP         | Korean   | F          |        6.5 | Northern Mariana Islands |
| PRK         | Korean   | T          |       99.9 | North Korea              |
| USA         | Korean   | F          |        0.3 | United States            |
+-------------+----------+------------+------------+--------------------------+
```


여기서는 결합 조건으로 'CountryCode(국가코드)', countrylanguage 테이블에서 '모든 열(*)', country 테이블에서 'name열'을 가져오고 있다. 이처럼 2개 테이블의 열이 어느 테이블에서 온 것인지를 구별하고 싶을 때는 테이블명을 추가하면 된다. 즉, '테이블명.열명'과 같이 지정한다. 2개의 테이블에 똑같은 이름의 열이 있는 경우에는 필수고, 한쪽 테이블에만 존재하는 열이라면 생략해도 괜찮다.


결합한 결과에 대해서는 보통의 단일 테이블 조작과 동일하게 WHERE, ORDER BY, LIMIT나 GROUP BY, HAVING을 지정할 수도 있다.


```
SELECT 선택하고 싶은 열 리스트 FROM 첫 번째 테이블명 INNER JOIN 두 번째 테이블명 ON 결합 조건;
```


### 외부결합이란
실무에서는 한 쪽 테이블을 기준으로 전체 행을 표시하고 다른 테이블은 값이 있으면 표시하고 싶은 경우가 자주 있다. 예를 들어, 행수(기준으로 삼을 테이블의 행수)를 고정해 화면에 표시하고 싶을 때나 업무 흐름에서 우선은 기준이 되는 테이블에 데이터를 등록하고 세부사항은 다른 테이블에 순차적으로 지정하고 싶은 경우 등이다. 이럴 때는 '외부결합(Outer Join)'을 이용한다
```
SELECT 선택하고 싶은 열의 리스트 FROM 첫 번째 테이블명 LEFT OUTER JOIN 두 번째 테이블명 ON 결합 조건;
```


LEFT OUTER 키워드를 사용한다. LEFT, 즉 2개의 테이블 중 왼쪽 테이블(첫 번째 테이블)의 전체 행이 표시되고, 다른 테이블의 행 데이터는 결합 조건과 일치할 때 그 값이 되고, 일치하는 것이 없으면 'NULL'이 된다.


![외부결합](https://github.com/0jun0815/YJStudy/blob/master/데이터베이스/SQL%20문의%20기본%20-%20SELECT%20문의%20이해/images/외부결합.jpg)


&nbsp;

외부결합은 첫 번째 테이블뿐만 아니라 두 번째 테이블을 기준으로 삼는 'RIGHT OUTER JOIN'라는 지정도 가능하다. 이는 기준이 되는 테이블이 두 번째 테이블이 된다는 것 외에는 LEFT OUTER JOIN과 똑같다. 하지만 특별히 이유가 없는(예를 들면, 쿼리의 자동 생성으로 첫 번째 테이블, 두 번째 테이블의 쿼리에서 순서를 변경할 수 없는 경우 등) 한은 LEFT OUTER JOIN 쪽이 알기 쉬워서 자주 사용된다.




&nbsp;
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### 출처: [데이터베이스 첫걸음](http://book.naver.com/bookdb/book_detail.nhn?bid=11154846)

