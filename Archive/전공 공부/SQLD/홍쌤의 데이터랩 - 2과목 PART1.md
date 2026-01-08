
# 관계형 데이터베이스 개요
- 관계형 데이터베이스 구성 요소
	- 계정: 데이터의 접근 제한
	- 테이블: 데이터가 저장되는 형식
	- 스키마: 테이블의 기본적인 구조 정의

- 테이블
	- 하나의 테이블은 반드시 하나의 계정(유저) 소유여야 함
	- 소유자가 다른 경우 테이블명 중복 가능
	- 데이터의 입력, 삭제는 행 단위
		- 특정 값 수정은 행 단위일 필요 X

- SQL
	- 데이터 정의 언어 (DDL)
	- 데이터 조작 전어 (DML)
	- 데이터 제어 언어 (DCL)

- 데이터 무결성
	- 개체 무결성: 기본키는 NULL이나 중복값을 가질 수 없음
	- 참조 무결성: 외래키 값은 NULL이거나 참조 테이블의 기본키 값과 동일해야 함
	- 도메인 무결성: 주어진 속성 값이 정의된 도메인에 속한 값이어야 함
	- NULL 무결성: 특성 속성에 대해 NULL을 허용하지 않음
	- 고유 무결성: 특성 속성에 대해 값이 중복되지 않음
	- 키 무결성: 하나의 릴레이션에는 적어도 하나의 키가 존재해야 함
- ERD
	- 테이블 간 서로의 상관 관계를 그림으로 표현한 것
	- 구성요소: 엔터티, 관계, 속성

# SELECT문
- SQL 종류
	- DDL: CREATE, ALTER, DROP, TRUNCATE
		- auto commit이 되는 특성을 가짐
			- commit: SQL 트랜잭션의 모든 변경 사항을 영구적으로 저장하는 것
		- TRUNCATE은 auto commit이기 때문에 DDL에 속함
	- DML: INSERT, DELETE, UPDATE, MERGE
	- DCL: GRANT, REVOKE
	- TCL: COMMIT, ROLLBACK
	- DQL: SELECT

- SELECT문 구조
	- SELECT * | 컬럼명 | 표현식
	- FROM 테이블명 또는 뷰명
	- WHERE 조회 조건
	- GROUP BY 그룹핑컬럼명
	- HAVING 그룹핑 필터링 조건
	- ORDER BY 정렬컬럼명

- SELECT문의 내부 파싱 순서
	- FROM > WHERE > GROUP BY > HAVING > SELECT > ORDER BY

- SELECT 절
	- 표시할 대상 컬럼에 alias(별칭) 지정 가능
		- SELECT 문보다 늦게 수행되는 ORDER BY 절에서만 별칭 사용 가능
	- 별칭에 쌍따옴표 전달이 필요한 경우
		- 공백 포함
		- 특수문자 포함("\_" 제외)
		- 대소문자를 그대로 표현하고 싶을 때

- FROM 절
	- 별칭 선언 가능 (AS 쓰지 않음)
		- 별칭 선언 시 컬럼 구분자는 별칭으로만 전달

# 함수
- input, output value의 관계를 정의한 객체
- from 절을 제외한 모든 절에서 사용 가능
- 단일행 함수: input과 output이 1:1
- 복수행 함수: input과 output이 N:1
  -> 그룹함수, 집계함수

- 함수 종류
	- 문자형 함수
	- 숫자형 함수
	- 날짜형 함수
	- 변환 함수
		- 값의 데이터 타입을 변환
	- 그룹 함수
	- 그 외 일반 함수
		- DECODE(대상, 값1, 리턴1, 값2, 리턴2, ..., 그 외 리턴)
			- 대상과 값을 비교해서 같으면 해당 리턴값 리턴
		- NVL(대상, 치환값)
			- 대상이 널이면 치환값으로 치환해서 리턴
		- NVL2(대상, 치환값1, 치환값2)
			- 대상이 널이 아니면 치환값1, 널이면 치환값2로 리턴
		- COALESCE(대상1, 대상2, ..., 그 외 리턴)
			- 대상n에서 널인 값은 대상n+1의 (같은 행의) 값으로 대체
			- 그 외 리턴값 생략 시 널 리턴
		- CASE
			- CASE WHEN 사이에 대상이 존재하는 경우
				- 예시) CASE 대상 WHEN 상수 THEN '문자'
				  -> 대상과 상수 equal 비교

# WHERE절
- 조건에 맞는 데이터만 조회하고 싶을 경우 사용
- AND와 OR로 조건 연결
- NULL 조회 시 IS NULL/ IS NOT NULL 연산자 사용
- 연산자 종류
	- BETWEEN a AND b
		- a <=, <= b인 경우 조회
	- LIKE
		- %: 임의의 여러 자리수 문자
		- \_: 임의의 한 자리수 문자
- 문자 상수의 대소문자 구분
	- ORACLE: O
	- MSSQL: X

# GROUP BY절
- GROUP BY의 컬럼값 별로 집계 함수를 사용하지 않은 컬럼은 SELECT에 사용 불가
	- ex.
	  SELECT DEPTNO, MAX(SAL), ENAME
	  FROM EMP
	  GROUP BY DEPTNO;

# HAVING절
- 그룹 함수 결과를 포함한 조건절
- WHERE절을 사용하여 그룹을 제한할 수 없으므로 HAVING절 사용
- GROUP BY절 뒤에 쓰는 것을 권장
- WHERE, HAVING절 동시 사용
	- WHERE절을 통해 원하는 데이터만 필터링
	  -> GROUP BY절로 그룹연산 수행
	  -> HAVING절에서 만족하는 데이터만 선택하여 출력

# ORDER BY절
- 출력되는 행의 순서를 사용자가 변경하고자 할 때 사용
- 유일하게 SELECT절에 정의한 컬럼 별칭 사용 가능
- SELECT절에 선언된 순서대로의 숫자로도 사용 가능
- 복합정렬
	- 먼저 정렬한 값의 동일한 결과가 있을 경우 추가적으로 정렬 가능

# 조인
- 여러 테이블의 데이터를 사용하여 동시 출력하거나 참조할 때 사용
- FROM절에 조인할 테이블 나열
- 동일한 열 이름 존재 -> 테이블 이름이나 Alias 붙임
- 조건의 형태에 따라
	- EQUI JOIN
	- NON EQUI JOIN
		- <, >, BETWEEN A AND B
- 조인 결과에 따라
	- INNER JOIN
	- OUTER JOIN
	- NATURAL JOIN
	- CROSS JOIN
	- SELF JOIN

# 표준조인
- INNER JOIN
	- (ANSI 표준)
		- FROM절: 테이블 사이에 조인 종류 명시
		- USING이나 ON 조건절을 필수적으로 사용
		- USING
			- 조인할 컬럼명이 같을 경우 사용
			- 접두사 불가
			- 괄호 필수
	- (ORACLE 표준)
		- FROM절: 테이블을 컴마로 구분
		- WHERE절: 조인 조건 나열
		- INNER JOIN이 기본 조인 연산
- NATURAL JOIN
	- 동일한 이름을 가지는 모든 컬럼들에 대해 EQUI JOIN을 수행
	- USING, ON, WHERE 절에서 조건 정의 불가
	- 데이터 유형이 동일해야 하며 접두사를 사용 불가
- CROSS JOIN
	- JOIN 조건이 없는 경우 생성 가능한 모든 조합 출력
- OUTER JOIN
	- JOIN조건에서 동일한 값이 없는 행도 반환할 때 사용
	- LEFT OUTER JOIN
		- 왼쪽 테이블 기준
		- 우측 값에서 같은 값이 없는 경우 NULL 값으로 출력
		- (ORACLE 표준)
			- WHERE절에 조인 조건을 작성
			- WHERE절에 LEFT OUTER JOIN을 기술 X
			- 기준 테이블이 아닌 테이블 조건 컬럼 뒤에 (+)
	- FULL OUTER JOIN
		- LEFT, RIGHT의 UNION 연산 리턴과 동일
		- ORACLE 표준에는 없음
- SELF JOIN
