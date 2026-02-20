[https://school.programmers.co.kr/learn/courses/30/lessons/131532](https://school.programmers.co.kr/learn/courses/30/lessons/131532)
```sql
SELECT
    YEAR(O.SALES_DATE) AS YEAR, 
    MONTH(O.SALES_DATE) AS MONTH, 
    I.GENDER,
    COUNT(DISTINCT I.USER_ID) AS USERS -- 중복을 꼭 제거해야 한다.
    
FROM
    USER_INFO AS I
JOIN
    ONLINE_SALE AS O
ON 
    I.USER_ID = O.USER_ID

WHERE
    I.GENDER IS NOT NULL

GROUP BY
    YEAR(O.SALES_DATE), MONTH(O.SALES_DATE), I.GENDER

ORDER BY
    YEAR(O.SALES_DATE), 
    MONTH(O.SALES_DATE), 
    I.GENDER
```
### 🔗 풀이
1. 🤔 년, 월, 성별 별로 상품을 구매한 회원수를 집계
    - GROUP BY 년, 월, 성별

2. 🤔 이때, 성별 정보가 없는 경우 결과에서 제외
     - WHERE절에서 필터링?
### 🔗 배운점
1. 왜 중복을 제거해야 할까?
   > `DISTINCT`를 사용하지 않는다면 `COUNT()`는 구매한 회원 수가 아니라 총 구매 건수가 된다.
  - `JOIN` 직후 테이블 상황

      | **SALES_DATE** | **USER_ID (회원번호)** | **GENDER (성별)** |
      | --- | --- | --- |
      | 2024-01-05 | 1 | M |
      | 2024-01-10 | 1 | M |
      | 2024-01-15 | 1 | M |
      | 2024-01-20 | 2 | M |

    1. 이 상황에서 그냥 `COUNT(I.USER_ID)`를 사용하면 행이 4개이기 때문에 결과는 4이다.
    2. 하지만 `COUNT(DISTINCT I.USER_ID)`처럼 중복을 제거하면, `USER_ID`는 1,2 두 개이기 때문에 결과는 2이다.
