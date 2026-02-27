[https://school.programmers.co.kr/learn/courses/30/lessons/151141](https://school.programmers.co.kr/learn/courses/30/lessons/151141)
```sql
WITH
    DISCOUNT_TABLE AS (
        SELECT
            HISTORY_ID,
            (
            CASE
                WHEN DATEDIFF(END_DATE, START_DATE) + 1 >= 90 THEN '90일 이상'
                WHEN DATEDIFF(END_DATE, START_DATE) + 1 >= 30 THEN '30일 이상'
                WHEN DATEDIFF(END_DATE, START_DATE) + 1 >= 7 THEN '7일 이상'
                ELSE 'NONE'
            END
            ) AS TYPE,
            DATEDIFF(END_DATE, START_DATE) + 1 AS DUARING
        FROM
            CAR_RENTAL_COMPANY_RENTAL_HISTORY
    )

SELECT
    H.HISTORY_ID,
    (
        ROUND((C.DAILY_FEE * (1 - (IFNULL(P.DISCOUNT_RATE,0)/100)))  * D.DUARING)
    ) AS FEE

FROM
    CAR_RENTAL_COMPANY_CAR AS C
JOIN
    CAR_RENTAL_COMPANY_RENTAL_HISTORY AS H
ON
    C.CAR_ID = H.CAR_ID
JOIN
    DISCOUNT_TABLE AS D
ON
    D.HISTORY_ID = H.HISTORY_ID
LEFT JOIN
    CAR_RENTAL_COMPANY_DISCOUNT_PLAN AS P
ON
    P.DURATION_TYPE  = D.TYPE AND P.CAR_TYPE = C.CAR_TYPE

WHERE
    C.CAR_TYPE = '트럭'
    
ORDER BY
    FEE DESC,
    HISTORY_ID DESC
```
### 🔗 풀이

### 🔗 배운점
1. `LEFT JOIN`을 해야 한다는 논리적 사고 흐름
   > 나는 대여 기록(`HISTORY`)에 할인 정책(`DISCOUNT_PLAN`)을 갖다 붙여야 한다.
   - 내 대여 기록 중에는 90일, 30일, 7일 이상도 있지만, '7일 미만(`NONE`)'인 데이터도 분명히 존재
   - 붙일 데이터(상대방)의 상태 확인: 할인 정책 테이블에는 '7일, 30일, 90일 이상'에 대한 할인율만 있고, '`NONE`(7일 미만)'에 대한 할인율 데이터는 아예 존재하지 않는다.
   - `INNER JOIN`을 쓴다면? 일반 조인은 '양쪽 테이블에 모두 존재하는 짝'만 데려온다. 따라서 7일 미만 대여 기록은 짝을 찾지 못하고 누락된다.
   - 그래서 `DISCOUNT_TABLE`의 `NONE`을 살리기 위해 `LEFT JOIN`해줘야 한다.
2. `IFNULL`을 써야한다는 논리적 사고 흐름
   - 1번의 이유로 7일 미만 대여 기록에는 `NULL`값이 붙여진다. 계산식에 `NULL`이 들어가는 순간 `NULL`이 반환되기 떄문에 `IFNULL()`처리를 해줘야 한다.
