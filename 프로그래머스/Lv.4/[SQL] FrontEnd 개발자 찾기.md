[https://school.programmers.co.kr/learn/courses/30/lessons/276035?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/276035?language=mysql)
```sql
SELECT
    ID,
    EMAIL,
    FIRST_NAME,
    LAST_NAME

FROM
    DEVELOPERS

WHERE
    (SKILL_CODE & (
        SELECT 
            SUM(CODE) -- 프론트엔드 모든 스킬코드를 더한다.
        FROM
            SKILLCODES
        WHERE
            CATEGORY = 'Front End'
         )
    ) > 0
    
ORDER BY
    ID
```
### 🔗 풀이
1. 🤔 Front End 스킬이란?
    - `CATEGORY`가 Front End인 스킬들

2. 🤔 구현 순서가 어떻게 될까?
    1. 개발자의 `SKILL_CODE`와 `CODE`컬럼을 비트 연산했을 때 1이 나온다면 해당 스킬을 보유하고 있는 것이다.
    2. 그 개발자의 정보를 조회하면 된다.
    
3. 🤔 그러면 비트 연산은 어떤식으로 구현해야 하는가?
    - `WHERE`절에서 서브 쿼리
    - 서브 쿼리가 의미하는 것은?
        - 카테고리가 front end인 모든 행의 `CODE`값을 조회한다.
### 🔗 배운점
1. 비트 연산은 더한 후 계산해도 된다. 2의 거듭제곱이기 때문이다.
