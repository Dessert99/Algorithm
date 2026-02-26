[https://school.programmers.co.kr/learn/courses/30/lessons/284528?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/284528?language=mysql)
```sql
WITH GRADE_TABLE AS (
    -- HR_GRADE에서 사원 번호 별로 묶어서 등급까지 추가함
    SELECT
        EMP_NO,
        (
            CASE
                WHEN AVG(SCORE) >= 96 THEN 'S'
                WHEN AVG(SCORE) >= 90 THEN 'A'
                WHEN AVG(SCORE) >= 80 THEN 'B'
                ELSE 'C'
            END
        ) AS GRADE
    FROM
        HR_GRADE
    GROUP BY
        EMP_NO
)

SELECT 
    EMP.EMP_NO,
    EMP.EMP_NAME,
    -- 평가등급
    G.GRADE,
    -- 성과금
    (
        CASE
            WHEN G.GRADE = 'S' THEN EMP.SAL * 0.2
            WHEN G.GRADE = 'A' THEN EMP.SAL * 0.15
            WHEN G.GRADE = 'B' THEN EMP.SAL * 0.1
            ELSE 0
        END
    ) AS BONUS
    
FROM
    HR_EMPLOYEES AS EMP
JOIN
    GRADE_TABLE AS G
ON
    EMP.EMP_NO = G.EMP_NO
   
ORDER BY
    EMP.EMP_NO
```
### 🔗 풀이
1. 🤔 평가등급과 성과금
    - `SELECT`절에서 `CASE WHEN` 사용하면 될 것 같다.
2. 🤔 일단 모든 사원의 행이 필요하다.
3. 🤔 `HR_DEPARTMENT` 테이블이 필요할까?
    - `EMP_NO`, `EMP_NAME` 컬럼은 `HR_EMPLOYEES`테이블에 존재한다.
    - `GRADE`, `BONUS` 컬럼에서 필요한 데이터
        1. `HR_GRADE`테이블의 `SCORE`컬럼
        2. `HR_EMPLOYEES`테이블의 `SAL`컬럼
    - 필요없을 것 같다. 
4.🤔 `GRADE_TABLE`을 만드는 이유
    - 우리가 계산해야 하는 것은 평가 등급과 성과금이다. 하지만 이 둘을 동시에 계산하지는 못한다.
    - 성과금을 계산하기 위해서는 평가 등급이 필요하다. 우선순위가 존재.
    - 그래서 우선 평가 등급 컬럼이 있는 테이블을 만들고, 이를 이용해서 성과금을 계산하는 방식임.
### 🔗 배운점
1. 새로운 컬럼 만들기 - `WITH`
  - `WITH`문법을 사용한 예시이다. `HR_GRADE` 테이블에서 필요한 컬럼만 추출하고, `GRADE`컬럼을 추가해 새로운 테이블을 만들면 나중에 조회하거나, 연봉을 계산할 때 편해진다.
