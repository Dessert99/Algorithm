[https://school.programmers.co.kr/learn/courses/30/lessons/157340](https://school.programmers.co.kr/learn/courses/30/lessons/157340)
```sql
-- 2022-10-16에 대여 중인 자동차를 모아놓은 테이블
WITH BETWEEN_10_16 AS (
    SELECT
        CAR_ID
    FROM
        CAR_RENTAL_COMPANY_RENTAL_HISTORY
    WHERE
        '2022-10-16' BETWEEN START_DATE AND END_DATE
)

SELECT
    CAR_ID,
    (CASE
        -- 현재 CAR_ID는 그룹화 되어있기 때문에 IN연산자로 판단한다.
        WHEN CAR_ID IN (SELECT CAR_ID FROM BETWEEN_10_16) THEN '대여중'
        ELSE '대여 가능'
    END)
    AS AVAILABILITY

FROM
    CAR_RENTAL_COMPANY_RENTAL_HISTORY
    
GROUP BY
    CAR_ID
    
ORDER BY
    CAR_ID DESC
```
### 🔗 풀이
1. 🤔 2022년 10월 16일에 대여 중인 자동차
    - START_DATE, END_DATE 사이에 2022-10-16가 존재하는 행

### 🔗 배운점
1. GROUP BY를 썼기 때문에 START_DATE, END_DATE는 현재 여러 개가 묶여 있다.
    - 서브 쿼리를 써야 하는 이유이다.
