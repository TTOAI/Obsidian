- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/133027
    
- 문제 유형: `JOIN`
    
- 내 풀이
```sql
SELECT F.FLAVOR
FROM FIRST_HALF F
JOIN (
    SELECT FLAVOR, SUM(TOTAL_ORDER) AS TOTAL
    FROM JULY
    GROUP BY FLAVOR
) J
ON F.FLAVOR = J.FLAVOR
ORDER BY F.TOTAL_ORDER + J.TOTAL DESC
LIMIT 3
```

---

- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/59042
    
- 문제 유형: `JOIN`
    
- 내 풀이
```sql
SELECT O.ANIMAL_ID, O.NAME
FROM ANIMAL_OUTS O
LEFT JOIN ANIMAL_INS I ON O.ANIMAL_ID = I.ANIMAL_ID
WHERE I.ANIMAL_ID IS NULL
```

- ChatGPT 모범 풀이
```sql
SELECT O.ANIMAL_ID, O.NAME
FROM ANIMAL_OUTS O
LEFT JOIN ANIMAL_INS I ON O.ANIMAL_ID = I.ANIMAL_ID
WHERE I.ANIMAL_ID IS NULL
ORDER BYY O.ANIMAL_ID;
```

---

- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/59043
    
- 문제 유형: `JOIN`
    
- 내 풀이
```sql
SELECT I.ANIMAL_ID, I.NAME
FROM ANIMAL_OUTS O, ANIMAL_INS I
WHERE I.DATETIME > O.DATETIME
ORDER BY I.DATETIME
```
-> 틀림

- 틀린 이유:
	- `FROM ANIMAL_OUTS O, ANIMAL_INS I` → `JOIN` 조건이 없음.
	- OUTS의 모든 행 × INS의 모든 행 → 가능한 모든 조합을 다 만들어버림.
	- `JOIN` 조건을 추가해야 함.

- ChatGPT 모범 풀이
```sql
SELECT I.ANIMAL_ID, I.NAME
FROM ANIMAL_OUTS O
JOIN ANIMAL_INS I
ON O.ANIMAL_ID = I.ANIMAL_ID
WHERE I.DATETIME > O.DATETIME
ORDER BY I.DATETIME
```

---

