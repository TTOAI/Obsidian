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
	- 보통 SUM, COUNT, AVG, MAX, MIN 같은 집계 함수와 함께 사용
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
	- SUM(col): 합계
	- COUNT(col): 개수
	- AVG(col): 평균
	- MAX(col):최댓값
	- MIN(col):최솟값

## JOIN

- 언제 사용?
	- 테이블이 분리되어 있고
	- 서로 연관된 데이터를 한 번에 보고 싶을 때

