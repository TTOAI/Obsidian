- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/133027
    
- 문제 유형: `JOIN`
    
- 내 풀이
```sql

```
    
- 모범 답안
```sql

```
    

---

# 핵심 개념 정리

## GROUP BY

- 언제 사용?
	- 같은 값을 기준으로 여러 행을 하나로 묶을 때
	- 보통 `SUM`, `COUNT`, `AVG`, `MAX`, `MIN` 같은 집계 함수와 함께 사용
	- 예시 상황:
		- 상품별 총 판매량
		- 사용자별 주문 횟수

- 기본 문법
```sql
SELECT 그룹기준컬럼, 집계함수(컬럼)
FROM 테이블
GROUP BY 그룹기준컬럼;
```

## 집계 함수

- 언제 사용?
	- 여러 행을 하나의 값으로 만들 때

- 주요 함수
	- `SUM(col)`: 합계
	- `COUNT(col)`: 개수
	- `AVG(col)`: 평균
	- `MAX(col)`:최댓값
	- `MIN(col)`:최솟값

## JOIN

- 언제 사용?
	- 테이블이 분리되어 있고
	- 서로 연관된 데이터를 한 번에 보고 싶을 때

- 기본 형태 (`INNER JOIN`)
```sql
SELECT ...
FROM A
JOIN B ON A.컬럼 = B.컬럼;
```

## 파생 컬럼

- 언제 사용?
	- 기존 컬럼을 이용해서 새로운 의미의 값을 만들 때

- 사용 예시
```sql
SELECT col1 + col2 AS new_name
FROM table;
```

## ORDER BY

- 언제 사용?
	- 결과를 특정 기준으로 정렬하고 싶을 때

- 기본 문법
```sql
ORDER BY col1 [ASC | DESC], col2 [ASC | DESC], ...;
```

## LIMIT

- 언제 사용?
	- 상위 N개
	- 미리보기
	- 랭킹 문제

- 문법
```sql
LIMIT 숫자;
```

## 서브쿼리 / 인라인 뷰

- 언제 사용?
	- 집계 결과를 하나의 테이블처럼 쓰고 싶을 때
	- `JOIN` 전에 미리 정리하고 싶을 때

- 형태
```sql
FROM (
	SELECT FLAVOR, SUM(TOTAL_ORDER) AS TOTAL
	FROM JULY
	GROUP BY FLAVOR
) J
```

## SQL 실행 순서

1. FROM / JOIN
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. ORDER BY
7. LIMIT

- 집계 전에 WHERE, 집계 후 조건은 HAVING
- SELECT에서 만든 별명은 ORDER BY에서 사용 가능
