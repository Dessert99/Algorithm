[https://school.programmers.co.kr/learn/courses/30/lessons/131534?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/131534?language=mysql)

### before
```sql
SELECT
    YEAR(SALES_DATE) AS YEAR,
    MONTH(SALES_DATE) AS MONTH,
    -- 중복을 제거한 구매한 이력이 있는 유저 수
    COUNT(DISTINCT INFO.USER_ID) AS PURCHASED_USERS,
    -- 비율(반올림)
    ROUND(
        (COUNT(DISTINCT INFO.USER_ID) /
           (
               SELECT COUNT(*)
                FROM USER_INFO
                WHERE YEAR(JOINED) = 2021
           ) 
        ), 1 ) AS PUCHASED_RATIO
    
FROM
    USER_INFO AS INFO
JOIN
    ONLINE_SALE AS SALE
ON
    INFO.USER_ID = SALE.USER_ID
    
WHERE
    YEAR(INFO.JOINED) = 2021

GROUP BY
    YEAR(SALES_DATE),
    MONTH(SALES_DATE)
    
ORDER BY
    YEAR(SALES_DATE),
    MONTH(SALES_DATE)
```
### after
```sql
WITH USERS_2021 AS (
    -- 2021년에 가입한 유저 목록만 미리 추출
    SELECT USER_ID
    FROM USER_INFO
    WHERE YEAR(JOINED) = 2021
)

SELECT
    YEAR(SALE.SALES_DATE) AS YEAR,
    MONTH(SALE.SALES_DATE) AS MONTH,
    -- 구매 유저 수 집계
    COUNT(DISTINCT SALE.USER_ID) AS PURCHASED_USERS,
    -- 비율 계산: 분모를 USERS_2021의 총 개수로 바로 구함
    ROUND(
        COUNT(DISTINCT SALE.USER_ID) / (SELECT COUNT(*) FROM USERS_2021), 
        1
    ) AS PURCHASED_RATIO
    
FROM
    ONLINE_SALE AS SALE
JOIN
    USERS_2021 AS U21
ON
    SALE.USER_ID = U21.USER_ID

-- 별칭 사용 가능
GROUP BY
    YEAR, 
    MONTH

-- 별칭 사용 가능
ORDER BY
    YEAR, 
    MONTH;
```

### 🔗 풀이
1. 🤔 2021년에 가입한 전체 회원들
    - YEAR(JOINED) = 2021

2. 🤔 상품을 구매한 회원수와 상품을 구매한 회원의 비율
    - 상품을 구매한 회원수
    - 상품을 구매한 회원의 비율

3. 🤔 상품을 구매한 회원의 비율은 소수점 두번째자리에서 반올림

4. 🤔 지금 내가 알아야 할 것들
    1. ONLINE_SALE 테이블에서 2021년에 가입한 회원 목록
    2. 중복을 제거한 USER_ID의 수
    3. 구매한 사람, 구매하지 않은 사람 비율 구하기
    4. 년, 월별로 출력

5. 🤔 JOIN을 하는가?
    - USER_ID 기준

6. 🤔 서브 쿼리를 해야 하는가?
    - SELECT절에서 해야할 듯?
### 🔗 배운점
1. 서브 쿼리를 따로 빼놓으면 코드가 간결해진다.
2. `SELECT`절에서 꼭 , 으로 구분해주자.
3. `SELECT`절에서 선언한 별칭은 `GROUP BY` `ORDER BY`에서도 사용이 가능하다.
