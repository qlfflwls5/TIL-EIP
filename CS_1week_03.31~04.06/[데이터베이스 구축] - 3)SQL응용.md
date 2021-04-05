# [데이터베이스 구축] - 3)SQL응용







## 1. SQL의 개념

### 1.1 SQL(Structured Query Language)의 개요

+ 국제 표준 데이터베이스 언어, **관계형 데이터베이스**(RDB)를 지원하는 언어
+ 관계대수와 관계해석을 기초로 한 혼합 데이터 언어
+ **질의어**지만 **데이터 구조의 정의**, **데이터 조작**, **데이터 제어 기능**까지 갖춤
  + 질의어: 비절차어의 일종으로 사용자들이 DB를 대화식으로 이용할 수 있게 한 언어





### 1.2 SQL의 분류

+ DDL(Data Define Language)

  + SCHEMA, DOMAIN, TABLE, VIEW, INDEX를 정의하거나 변경 또는 삭제할 때 사용하는 언어

  + 논리적 데이터 구조와 물리적 데이터 구조의 사상을 정의

  + **CREATE**

    + 위의 것들을 정의한다.

  + **ALTER**

    + TABLE에 대한 정의를 변경한다.

  + **DROP**

    + 위의 것들을 삭제한다.

    

+ DML(Data Manipulation Language)

  + 데이터베이스 사용자가 응용 프로그램이나 질의어를 통하여 저장된 데이터를 실질적으로 처리하는 데 사용하는 언어
  + 데이터베이스 사용자와 데이터베이스 관리 시스템 간의 인터페이스를 제공한다.
  + **SELECT**
    + 테이블에서 조건에 맞는 튜플 조회
  + **INSERT**
    + 테이블에 새로운 튜플 삽입
  + **DELETE**
    + 테이블에서 조건에 맞는 튜플 삭제
  + **UPDATE**
    + 테이블에서 조건에 맞는 튜플 내용 변경



+ DCL(Data Control Language)
  + 데이터의 보안, 무결성, 회복, 병행 수행 제어 등을 정의하는 데 사용되는 언어
  + 데이터베이스 관리자가 데이터 관리를 위한 목적으로 사용
  + **COMMIT**
    + 명령에 의해 수행된 결과를 실제 **물리적 디스크**로 저장, DB 조작 작업이 정상적으로 완료되었음을 관리자에게 알려줌
  + **ROLLBACK**
    + 데이터베이스 조작 작업이 비정상적으로 종료되었을 때 원래의 상태로 복구
  + **GRANT**
    + 데이터베이스 사용자에게 사용 권한을 부여
  + **REVOKE**
    + 데이터베이스 사용자의 사용 권한을 취소







## 2. DDL

### 2.1 DDL의 개념

+ DB구조, 데이터 형식, 접근 방식 등 DB를 구축하거나 수정할 목적으로 사용하는 언어





### 2.2 CREATE SCHEMA

+ Schema(스키마)

  + 데이터베이스의 구조와 제약 조건에 관한 전반적인 명세를 기술한 것(데이터 개체, 속성, 관계 등)
  + 스키마의 식별을 위해 **스키마명**과 **소유권자나 허가권자**를 정의

+ ```sql
  CREATE SCHEMA 스키마명 AUTHORIZATION 사용자_id;
  ```





### 2.3 CREATE DOMAIN

+ Domain(도메인)

  + 하나의 속성이 취할 수 있는 동일한 유형의 원자값들의 집합
    + 즉, 특정 속성에서 사용할 데이터의 범위를 사용자가 정의하는 사용자 정의 데이터 타입(ex) 정수에서 1~4의 범위만 사용하고 싶을 때)

+ ```sql
  CREATE DOMAIN 도메인명 [AS] 데이터_타입
  	[DEFAULT 기본값]
  	[CONSTRAINT 제약조건명 CHECK (범위값)];
  ```

+ ```sql
  CREATE DOMAIN SEX CHAR(1)
  	DEFAULT '남'
  	CONSTRAINT VALID-SEX CHECK (VALUE IN ('남', '여'));
  ```





### 2.4 CREATE TABLE

+ ```sql
  CREATE TABLE 테이블명
  	(속성명 데이터_타입 [DEFAULT 기본값][NOT NULL], ...
      [, PRIMARY KEY(기본키 속성명, ...)]
      [, UNIQUE(대체키_속성명, ...)]
      [, FOREIGN KEY(외래키_속성명, ...)]
      	[REFERENCES 참조테이블(기본키_속성명, ...)]
       	[ON DELETE 옵션]
       	[ON UPDATE 옵션]
      [, CONSTRAINT 제약조건명][CHECK (조건식)]);
  ```

+ ```sql
  CREATE TABLE 학생
  	(이름 VARCHAR(15) NOT NULL,
      학번 CHAR(8),
      전공 CHAR(5),
      성별 SEX,
      생년월일 DATE,
      PRIMARY KEY(학번),
      FOREIGN KEY(전공) REFERENCES 학과(학과코드)
      	ON DELETE SET NULL
      	ON UPDATE CASCADE,
      CONSTRAINT 생년월일제약
      	CHECK(생년월일>='1980-01-01'));
  ```

  + CONSTRAINT는 제약조건의 이름을 정하는 것이며, 따로 설정하지 않고 CHECK절로 조건만 명시해줘도 된다.

+ 기존 테이블의 정보를 이용해 새로운 테이블을 정의할 수도 있다.

  + ```sql
    CREATE TABLE 신규테이블명 AS SELECT 속성명[, 속성명, ...] FROM 기본테이블명;
    ```





### 2.5 CREATE VIEW

+ View

  + 하나 이상의 기본 테이블로부터 유도되는 이름을 갖는 가상 테이블
    + 뷰는 물리적으로 구현되지 않아 저장되지 않는다. 따라서, 뷰 이름을 사용하면 실행 시간에 뷰 정의가 대체되어 수행된다.

+ ```sql
  CREATE VIEW 뷰명[(속성명[, 속성명, ...])]
  AS SELECT문;
  ```

  + SELECT문을 서브 쿼리로 사용하여 SELECT문의 결과로서 뷰를 생성
  + SELECT문에는 UNION이나 ORDER BY절 사용 불가
  + 속성명을 기술하지 않으면 SELECT문의 속성명이 자동 사용됨

+ ```sql
  CREATE VIEW 안산고객(성명, 전화번호)
  AS SELECT 성명, 전화번호
  FROM 고객
  WHERE 주소='안산시';
  ```





### 2.6 CREATE INDEX

+ Index

  + 검색 시간을 단축시키기 위해 만든 보조적인 데이터 구조

+ ```sql
  CREATE [UNIQUE] INDEX 인덱스명
  ON 테이블명(속성명[ASC | DESC], ...)
  [CLUSTER];
  ```

+ ```sql
  CREATE UNIQUE INDEX 고객번호_idx
  ON 고객(고객번호 DESC);
  ```





### 2.7 ALTER TABLE

+ ```sql
  ALTER TABLE 테이블명 ADD 속성명 데이터_타입[DEFAULT '기본값'];
  ALTER TABLE 테이블명 ALTER 속성명 [SET DEFAULT '기본값'];
  ALTER TABLE 테이블명 DROP COLUMN 속성명 [CASCADE];
  ```

  + ADD

    + 새로운 속성을 추가할 때 사용

  + ALTER

    + 특정 속성의 Default 값을 변경할 때 사용

    + ```sql
      ALTER TABLE 학생 ALTER 학번 VARCHAR(10) NOT NULL;
      ```

  + DROP COLUMN

    + 특정 속성을 삭제할 때 사용





### 2.8 DROP

+ 스키마, 도메인, 테이블, 뷰, 인덱스, 제약 조건등을 제거하는 명령문

+ ```sql
  DROP SCHEMA 스키마명 [CASCADE | RESTRICT];
  ...
  DROP CONSTRAINT 제약조건명;
  ```

  + CASCADE
    + 제거할 요소를 참조하는 다른 모든 객체를 함께 제거
  + RESTRICT
    + 다른 개체가 제거할 요소를 참조중이면 제거를 취소

+ DROP은 삭제하려는 테이블과 함께 **그 테이블로부터 유도하여 만든 INDEX, VIEW도 모두 제거한다**.







## 3. DCL

### 3.1 DCL의 개념

+ 데이터의 보안, 무결성, 회복, 병행 제어 등을 정의하는 데 사용하는 언어





### 3.2 GRANT/REVOKE

+ ```sql
  GRANT 사용자등급 TO 사용자_ID_리스트 [IDENTIFIED BY 암호];
  REVOKE 사용자등급 FROM 사용자_ID_리스트;
  ```

+ ```sql
  GRANT RESOURCE TO NABI;
  REVOKE CONNECT FROM NABI;
  ```

  + RESOURCE
    + 데이터베이스 및 테이블 생성 가능자
  + CONNECT
    + 단순 사용자
  
+ ```sql
  GRANT 권한 ON 테이블 TO 사용자;
  ```

  + 테이블에 대한 권한을 부여. REVOKE도 마찬가지





### 3.3 COMMIT

+ 트랜잭션이 성공적으로 끝나면 DB가 새로운 일관성 상태를 가지기 위해 변경된 모든 내용을 DB에 반영할 때 사용한다.

+ COMMIT을 하지 않아도 DML문 실행 후 Auto Commit 기능을 설정할 수 있다.

+ ```sql
  COMMIT;
  ```





### 3.4 ROLLBACK

+ ROLLBACK은 **아직 COMMIT되지 않은 변경된 모든 내용**들을 취소하고 DB를 이전 상태로 되돌리는 명령이다.

+ 비정상적으로 종료된 경우 비일관성을 가지므로 이러한 트랜잭션은 롤백해야 한다.

+ ```sql
  ROLLBACK;
  ```





### 3.5 SAVEPOINT

+ SAVEPOINT는 트랜잭션 내에 ROLLBACK 할 위치인 저장점을 지정하는 명령어다.

+ ```sql
  SAVEPOINT S1;
  작업 수행
  ROLLBACK TO S1; -> S1때로 되돌아감
  ```







## 4. DML

### 4.1 삽입문(INSERT INTO)

+ 기본 테이블에 새로운 튜플을 삽입할 때 사용한다.

+ ```sql
  INSERT INTO 테이블명(속성명, ...)
  VALUES (데이터, ...)
  ```

+ SELECT문을 사용해서 다른 테이블의 검색 결과를 삽입할 수 있다.

  + ```sql
    INSERT INTO 편집부원(이름, 생일, 주소, 기본급)
    SELECT 이름, 생일, 주소, 기본급
    FROM 사원
    WHERE 부서='편집';
    ```





### 4.2 삭제문(DELETE FROM)

+ 기본 테이블에서 특정 튜플을 삭제할 때 사용한다.

+ ```sql
  DELETE FROM 테이블명 [WHERE 조건];
  ```





### 4.3 갱신문(UPDATE ~ SET ~)

+ 기본 테이블에 있는 튜플들 중에서 특정 튜플의 내용을 변경할 때 사용한다.

+ ```sql
  UPDATE 테이블명 SET 속성명=데이터, ... [WHERE 조건];
  ```







## 5. DML - SELECT

### 5.1 일반 형식

+ ```sql
  SELECT [PREDICATE] [테이블명.]속성명 [AS 별칭], ...
  [, 그룹함수(속성명) [AS 별칭]]
  [, Window함수 OVER (PARTITION BY 속성명1, 속성명2, ...
                   ORDER BY 속성명3, 속성명4, ...)]
  FROM 테이블명, ...
  [WHERE 조건]
  [GROUP BY 속성명, ...]
  [HAVING 조건]
  [ORDER BY 속성명 [ASC | DESC]];
  
  ```

  + PREDICATE

    + 불러올 튜플 수를 제한할 명령어를 기술한다.
    + ALL: 모든 튜플을 검색할 때, 주로 생략한다.
    + DISTINCT: 중복된 튜플이 있으면 그중 첫 번째 한 개만 검색한다.
    + DISTINCTROW: 중복된 튜플을 제거하고 한 개만 검색하지만 선택된 속성의 값이 아닌, 튜플 전체를 대상으로 한다.

    

+ 조건 연산자

  + 비교 연산자
    + =, <>, >, <, <=, >=
      + 같지 않다는 !=이 아니라 <>다.
  + 논리 연산자
    + AND, OR, NOT
  + LIKE 연산자
    + %(모든 문자를 대표), _(문자 하나를 대표), #(숫자 하나를 대표)

  + 연산자 우선순위는 산술 > 비교 > 논리

  

+ 예제) <사원> 테이블에서 '기본급'에 특별수당 10을 더한 월급을 "XX부서의 XXX의 월급 XXX" 형태로 출력

  + ```sql
    SELECT 부서+'부서의' AS 부서2, 이름+'월급' AS 이름2, 기본급+10 AS 기본급2 FROM 사원;
    ```



+ WHERE절의 조건은 AND와 OR로 연결 가능, NOT으로 부정 가능

  + LIKE를 활용해서 검색 가능 `WHERE 이름 LIKE "김%";`

  + BETWEEN으로 사이를 표현
    + `WHERE 생일 BETWEEN #01/01/69# AND #12/31/73#;`
      + 날짜 데이터는 숫자로 취급하지만 ''나 ##으로 묶어준다.
  + IS NULL로 NULL값 표현
    + `WHERE 주소 IS NULL;`
  + IN, NOT IN으로 존재 여부 표현





### 5.2 하위 질의

+ **조건절**에 주어진 질의를 먼저 수행하여 그 검색 결과를 조건절의 피연산자로 사용

+ '취미'가 "나이트댄스"인 사람의 '이름'과 '주소'를 검색하시오.

  + ```sql
    SELECT 이름, 주소 FROM 사원 WHERE 이름=(SELECT 이름 FROM 여가활동 WHERE 취미='나이트댄스');
    ```

+ 취미활동을 하지 않는 사원들을 검색하시오.

  + ```sql
    SELECT * FROM 사원 WHERE 이름 NOT IN (SELECT 이름 FROM 여가활동);
    ```

+ 취미활동을 하는 사원들의 부서를 검색하시오.

  + ```sql
    SELECT 부서 FROM 사원 WHERE EXISTS(SELECT 이름 FROM 여가활동 WHERE 여가활동.이름=사원.이름);
    ```

    + EXISTS()는 하위 질의로 검색된 결과가 존재하는지 확인할 때 사용한다.
    + 사원 테이블의 '이름'이 여가활동 테이블의 '이름'에도 있는지 확인한다.





### 5.3 복수 테이블 검색

+ '경력'이 10년 이상인 사원의 '이름', '부서', '취미', '경력'을 검색하시오.

+ ```sql
  SELECT 사원.이름, 사원.부서, 여가활동.취미, 여가활동.경력
  FROM 사원, 여가활동
  WHERE 여가활동.경력 >= 10 AND 사원.이름=여가활동.이름;
  ```

  



## 6. SELECT- 2

+ **그룹함수**: GROUP BY절에 지정된 그룹별로 속성의 값을 집계할 함수를 기술한다.
+ **WINDOW함수**: GROUP BY절을 이용하지 않고 속성의 값을 집계할 함수를 기술한다.
  + PARTITION BY: WINDOW함수가 적용될 범위로 사용할 속성을 지정한다.
  + ORDER BY: PARTITION 안에서 정렬 기준으로 사용할 속성을 지정한다.
+ **GROUP BY절**: 특정 속성을 기준으로 그룹화하여 검색할 때 사용한다. 일반적으로 GROUP BY절은 그룹 함수와 함께 사용된다.
+ **HAVING절**: GROUP BY와 함께 사용되며, 그룹에 대한 조건을 지정한다.

### 6.1 WINDOW 함수 이용 검색

+ '상여금' 테이블에서 '상여내역'별로 '상여금'에 대한 일련 번호를 구하시오. (단, 순서는 내림차순이며 속성명은 'NO'로 할 것)

+ ```sql
  SELECT 상여내역, 상여금, ROW_NUMBER() OVER (PARTITION BY 상여내역 ORDER BY 상여금 DESC) AS NO FROM 상여금;
  ```

+ ROW_NUMBER(): 윈도우별로 각 레코드에 대한 일련 번호를 반환

+ RANK(): 윈도우별로 순위를 반환하며, 공동 순위를 반영

+ DENSE_RANK(): 순위를 반환, 공동 순위 무시





### 6.2 GROUP BY

+ '상여금' 테이블에서 '부서'별 '상여금'의 평균

+ ```sql
  SELECT 부서, AVG(상여금) AS 평균 FROM 상여금 GROUP BY 부서;
  ```

+ '상여금' 테이블에서 '상여금'이 100 이상인 사원 2명 이상인 '부서'의 튜플 수

+ ```sql
  SELECT 부서, COUNT(*) AS 사원수 FROM 상여금 WHERE 상여금>=100 GROUP BY 부서 HAVING COUNT(*)>=2;
  ```

+ '상여금' 테이블의 '부서', '상여내역', 그리고 '상여금'에 대해 부서별 상여내역별 소계와 전체 합계를 구하시오.(단, 속성명은 '상여금합계'로 하고, ROLLUP함수를 사용할 것)

+ ```sql
  SELECT 부서, 상여내역, SUM(상여금) AS 상여금합계 FROM 상여금 GROUP BY ROLLUP(부서, 상여내역)
  ```

  + CUBE함수도 비슷하게 쓴다.





### 6.4 집합 연산자를 이용한 통합 질의

+ 집합 연산자를 사용하여 2개 이상의 테이블의 데이터를 하나로 통합한다.

+ ```sql
  SELECT 속성명, ...
  FROM 테이블명
  UNION | UNION ALL | INTERSECT | EXCEPT
  SELECT 속성명, ...
  FROM 테이블명
  [ORDER BY 속성명 [ASC | DESC]];
  ```

  + 두 개의 SELECT문에 기술한 속성들은 개수와 데이터 유형이 서로 동일해야 한다.

+ **UNION**(합집합)

  + 두 SELECT문의 조회 결과를 통합하여 모두 출력한다.
  + 중복된 행은 한 번만 출력한다.

+ **UNION ALL**(합집합)

  + 두 SELECT문의 조회 결과를 통합하여 모두 출력한다.
  + 중복된 행도 그대로 출력한다.

+ **INTERSECT**(교집합)

  + 두 SELECT문의 조회 결과 중 공통된 행만 출력한다.

+ **EXCEPT**(차집합)

  + 첫 번째 SELECT문의 조회 결과에서 두 번째 SELECT문의 조회 결과를 제외한 행을 출력한다.







## 7. DML - JOIN

### 7.1 JOIN의 개념

+ 2개의 테이블에 대해 연관된 튜플들을 결합하여, 하나의 새로운 릴레이션을 반환
  + INNER JOIN과 OUTER JOIN으로 구분
  + 일반적으로 FROM절에 기술





### 7.2 INNER JOIN

+ **EQUI JOIN**

  + JOIN 대상 테이블에서 공통 속성을 기준으로 '='비교에 의해 같은 값을 가지는 행을 연결하여 결과를 생성하는 JOIN 방법

  + 조건이 '='일 때 동일한 속성이 두 번 나타나게 되는데, 중복된 속성을 제거해 같은 속성을 한 번만 표기하는 방법을 **NATURAL JOIN**이라고 한다.

  + 연결 고리가 되는 공통 속성을 JOIN 속성이라고 한다.

  + ```sql
    SELECT [테이블명.]속성명, ...
    FROM 테이블명, ...
    WHERE 테이블명1.속성명 = 테이블명2.속성명;
    ```

  + ```sql
    SELECT [테이블명.]속성명, ...
    FROM 테이블명1 NATURAL JOIN 테이블명2;
    ```

  + ```sql
    SELECT [테이블명.]속성명, ...
    FROM 테이블명1 JOIN 테이블명2 USING(속성명);
    ```

  + '학생' 테이블과 '학과' 테이블에서 '학과코드'의 값이 같은 튜플을 JOIN하여 '학번', '이름', '학과코드', '학과명'을 출력하는 sql문

    + ```sql
      SELECT 학번, 이름, 학생.학과코드, 학과명
      FROM 학생, 학과
      WHERE 학생.학과코드 = 학과.학과코드;
      
      SELECT 학번, 이름, 학생.학과코드, 학과명
      FROM 학생 NATURAL JOIN 학과;
      
      SELECT 학번, 이름, 학생.학과코드, 학과명
      FROM 학생 JOIN 학과 USING(학과코드);
      ```



+ **NON-EQUI JOIN**

  + JOIN 조건에 '='조건이 아닌 나머지 비교 연산자 >, <, <>, >=, <=를 사용하는 JOIN

  + ```sql
    SELECT [테이블명.]속성명, ...
    FROM 테이블명1, 테이블명2, ...
    WHERE (NON-EQUI JOIN 조건);
    ```

  + ```sql
    SELECT 학번, 이름, 성적, 등급
    FROM 학생, 성적등급
    WHERE 학생.성적 BETWEEN 성적등급.최저 AND 성적등급.최고;
    ```





### 7.3 OUTER JOIN

+ 릴레이션에서 JOIN 조건에 만족하지 않는 튜플도 결과로 출력하기 위한 JOIN방법으로, LEFT OUTER JOIN, RIGHT OUTER JOIN, FULL OUTER JOIN이 있다.



+ **LEFT OUTER JOIN**

  + INNER JOIN의 결과를 구한 후, 우측 항 릴레이션의 어떤 튜플과도 맞지 않는 좌측 항의 릴레이션에 있는 튜플들에 **NULL값을 붙여서** INNER JOIN의 결과에 추가

  + ```sql
    SELECT [테이블명.]속성명, ...
    FROM 테이블명1 LEFT OUTER JOIN 테이블명2
    ON 테이블명1.속성명 = 테이블명2.속성명;
    
    SELECT [테이블명.]속성명, ...
    FROM 테이블명1, 테이블명2
    WHERE 테이블명1.속성명 = 테이블명2.속성명(+);
    ```



+ **RIGHT OUTER JOIN**

  + LEFT의 반대

  + ```sql
    SELECT [테이블명.]속성명, ...
    FROM 테이블명1 RIGHT OUTER JOIN 테이블명2
    ON 테이블명1.속성명 = 테이블명2.속성명;
    
    SELECT [테이블명.]속성명, ...
    FROM 테이블명1, 테이블명2
    WHERE 테이블명1.속성명(+) = 테이블명2.속성명;
    ```




+ **FULL OUTER JOIN**

  + LEFT + RIGHT

  + ```sql
    SELECT [테이블명.]속성명, ...
    FROM 테이블명1 FULL OUTER JOIN 테이블명2
    ON 테이블명1.속성명 = 테이블명2.속성명;
    ```





### 7.4 SELF JOIN

+ 같은 테이블에서 2개의 속성을 연결하여 EQUI JOIN을 하는 방법

+ ```sql
  SELECT [별칭1.]속성명, [별칭1.]속성명, ...
  FROM 테이블명1 [AS] 별칭1 JOIN 테이블명1 [AS] 별칭2
  ON 별칭1.속성명 = 별칭2.속성명;
  
  SELECT [별칭1.]속성명, [별칭1.]속성명, ...
  FROM 테이블명1 [AS] 별칭1, 테이블명1 [AS] 별칭2
  WHERE 별칭1.속성명 = 별칭2.속성명;
  ```

+ '학생' 테이블을 SELF JOIN하여 선배가 있는 학생과 선배의 '이름'을 표시하는 sql문

+ ```sql
  SELECT A.학번, A.이름, B.이름 AS 선배
  FROM 학생 A JOIN 학생 B
  ON A.선배 = B.학번;
  ```