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
    - SELECT절에서 CASE WHEN 사용하면 될 것 같다.
2. 🤔 일단 모든 사원의 행이 필요하다.
3. 🤔 HR_DEPARTMENT 테이블이 필요할까?
    - EMP_NO, EMP_NAME 컬럼은 HR_EMPLOYEES테이블에 존재한다.
    - GRADE, BONUS 컬럼에서 필요한 데이터
        1. HR_GRADE테이블의 SCORE컬럼
        2. HR_EMPLOYEES테이블의 SAL컬럼
    - 필요없을 것 같다. 
4. 🤔 연간 성과금이란?
    - 상반기, 하반기 평균 낸 점수로 등급을 매겨야 한다...

### 🔗 배운점
1. 새로운 컬럼 만들기 - `WITH`
  - `WITH`문법을 사용한 예시이다. `HR_GRADE` 테이블에서 필요한 컬럼만 추출하고, `GRADE`컬럼을 추가해 새로운 테이블을 만들면 나중에 조회하거나, 연봉을 계산할 때 편해진다.
