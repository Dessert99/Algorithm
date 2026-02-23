[https://school.programmers.co.kr/learn/courses/30/lessons/151138](https://school.programmers.co.kr/learn/courses/30/lessons/151138)
```sql
SELECT
    HISTORY_ID,
    CAR_ID,
    DATE_FORMAT(START_DATE, '%Y-%m-%d') AS START_DATE,
    DATE_FORMAT(END_DATE, '%Y-%m-%d') AS END_DATE,
    (
        CASE
            -- 대여일은 당일 포함이다.
            WHEN DATEDIFF(END_DATE, START_DATE) + 1 >= 30 THEN '장기 대여'
            ELSE '단기 대여'
        END
    ) AS RENT_TYPE

FROM
    CAR_RENTAL_COMPANY_RENTAL_HISTORY

WHERE
    -- 2022년 9월 기록
    START_DATE LIKE '2022-09%'

ORDER BY
    HISTORY_ID DESC
```
### 🔗 풀이

### 🔗 배운점
1. **`DATEDIFF`**
    
    ```sql
    **// DATEDIFF(end, start)**
    
    DATEDIFF('2024-01-05', '2024-01-01') //  → 4 
    ```
    - 이 문제처럼 대여일을 묻는 문제는 +1를 해줘야 한다. 당일도 포함해야 하기 때문이다.
