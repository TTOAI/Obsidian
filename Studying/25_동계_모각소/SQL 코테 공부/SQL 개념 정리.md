

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

---

## 집계 함수

- 언제 사용?
    - 여러 행을 하나의 값으로 만들 때
- 주요 함수
    - `SUM(col)`: 합계
    - `COUNT(col)`: 개수
    - `AVG(col)`: 평균
    - `MAX(col)`:최댓값
    - `MIN(col)`:최솟값

---

## INNER JOIN

- 언제 사용?
    - 테이블이 분리되어 있고
    - 서로 연관된 데이터를 한 번에 보고 싶을 때
- 기본 형태 (`INNER JOIN`)

```sql
SELECT ...
FROM A
JOIN B ON A.컬럼 = B.컬럼;
```

---

## 파생 컬럼

- 언제 사용?
    - 기존 컬럼을 이용해서 새로운 의미의 값을 만들 때
- 사용 예시

```sql
SELECT col1 + col2 AS new_name
FROM table;
```

---

## ORDER BY

- 언제 사용?
    - 결과를 특정 기준으로 정렬하고 싶을 때
- 기본 문법

```sql
ORDER BY col1 [ASC | DESC], col2 [ASC | DESC], ...;
```

---

## LIMIT

- 언제 사용?
    - 상위 N개
    - 미리보기
    - 랭킹 문제
- 문법

```sql
LIMIT 숫자;
```

---

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

---

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

---

## **OUTER JOIN**

- 언제 사용?
    - 한쪽 테이블에만 존재하는 데이터도 결과에 포함하고 싶을 때
    - “매칭되지 않은 행”도 버리지 않고 보고 싶을 때
    - 데이터 누락, 비교, 검증 문제에서 자주 사용
    - 예시 상황:
        - 주문하지 않은 회원 찾기
        - A 시스템에는 있지만 B 시스템에는 없는 데이터
- 개념
    - INNER JOIN: 양쪽 모두 있는 데이터만 (교집합)
    - OUTER JOIN: 한쪽 또는 양쪽을 전부 살림
- 종류
    - LEFT OUTER JOIN → 왼쪽 테이블 전부 유지
    - RIGHT OUTER JOIN → 오른쪽 테이블 전부 유지
    - FULL OUTER JOIN → 양쪽 전부 유지

---

## **LEFT JOIN (LEFT OUTER JOIN)**

- 언제 사용?
    - 기준 테이블의 모든 행을 유지하고 싶을 때
    - 연결된 데이터가 없어도 결과에 남기고 싶을 때
    - “기준 테이블 + (있으면) 연결 데이터” 구조
- 기본 문법

```
SELECT ...
FROM A
LEFT JOIN B ON A.컬럼 = B.컬럼;
```

- 결과 특징
    - A 테이블의 모든 행은 무조건 출력
    - B에 매칭되는 값이 없으면 B의 컬럼은 NULL
- 예시 상황
    - 전체 회원 + 주문 정보
    - 전체 상품 + 판매 기록

---

## **NULL**

- 언제 등장?
    - 값이 없음 / 알 수 없음 / 존재하지 않음
    - OUTER JOIN에서 매칭 실패한 컬럼에 자동으로 채워짐
    - 선택 입력값(이름 등)이 비어 있을 때
- NULL 판별 방법

```
컬럼 IS NULL
컬럼 IS NOT NULL
```

❌ 잘못된 예

```
컬럼 = NULL
컬럼 != NULL
```

- 특징
    - NULL은 값이 아니라 “상태”
    - 산술 연산하면 결과도 NULL
    - 비교 연산(=, <, >) 불가능 → IS NULL 사용

---

## **ANTI JOIN (**LEFT JOIN + IS NULL)

- 언제 사용?
    - A에는 있고, B에는 없는 데이터를 찾고 싶을 때
    - “존재하지 않는 관계”를 찾는 문제
    - 데이터 누락, 미처리 항목, 이상 데이터 검출
- 대표 예시
    - 주문하지 않은 고객
    - 댓글 없는 게시글
- 가장 많이 쓰는 패턴 (LEFT JOIN 방식)

```
FROM A
LEFT JOIN B ON A.id = B.id
WHERE B.id IS NULL
```

→ 의미: “A에는 있지만 B에는 없음”

---

## **OUTER JOIN 문제 사고 순서**

1. 누가 기준인가? (전부 살릴 테이블)
2. 그 테이블을 FROM 또는 LEFT 쪽에 둔다
3. 반대 테이블을 OUTER JOIN
4. 없는 쪽 컬럼에 IS NULL 조건
5. 필요한 컬럼 SELECT
6. 정렬 (ORDER BY)

---