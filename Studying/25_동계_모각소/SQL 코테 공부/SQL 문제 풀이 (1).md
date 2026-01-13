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
