[https://school.programmers.co.kr/learn/courses/30/lessons/299307?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/299307?language=mysql)
```sql
SELECT ID, 
        CASE 
            WHEN SIZE_OF_COLONY <= 100 THEN 'LOW'
            WHEN SIZE_OF_COLONY > 100 AND SIZE_OF_COLONY <=1000 THEN 'MEDIUM'
            WHEN SIZE_OF_COLONY > 1000 THEN 'HIGH'
         END AS SIZE -- 여기서 컬럼명을 지정한다.    

FROM ECOLI_DATA

ORDER BY ID
```
### 🔗 풀이
1. 조회
    - ID, SIZE
2. 테이블
    - ECOLI_DATA
3. 조건
4. 정렬
    - ID

💡 대장균 분류 방법
대장균 개체의 크기가

- 100 이하라면 'LOW'
- 100 초과 1000 이하라면 'MEDIUM'
- 1000 초과라면 'HIGH'

🤔 SIZE 컬럼
현재 ECOLI_DATA테이블에는 SIZE컬럼이 없다. 개체 크기를 나타내는 SIZE_OF_COLONY 컬럼이 있다. 어떻게 SIZE컬럼을 표시할까?

🤔 셀프 조인
하나의 테이블 내에 있는 컬럼들끼리 관계가 있을 때, 서로 다른 테이블인 것처럼 조인하는 기법이다. 현재 컬럼들끼리 관계가 있는가? 없어보인다. 우리는 SIZE_OF_COLONY에만 관심이 있다.

❓ CASE WHEN 조건문
SELECT문에서 개체 크기는 다양한 분류 방법을 가진다. SIZE_OF_COLONY를 조회하여, 조건에 따라 LOW, MEDIUM, HIGH를 표기하면 되지 않을까?

→ 이때 조건문으로 `SELECT`절에 `CASE WHEN` 조건문을 사용해보자.

### 🔗 배운점
1. `CASE WHEN` 기본
    
    ```sql
    CASE
        WHEN [조건1] THEN [반환값1]
        WHEN [조건2] THEN [반환값2]
        ELSE [기본값] -- 조건에 맞는 게 없을 때 반환 (생략 시 NULL)
    END AS [별칭]
    ```
    
    - `ESLE` 는 생략해도 좋지만 조건에 맞지 않으면 NULL을 반환한다.
2. 예시) 상황: "개발팀"과 "인사팀"의 인원수를 한 줄로 출력하고 싶을 때
    
    ```sql
    SELECT
        SUM(CASE WHEN dept = '개발팀' THEN 1 ELSE 0 END) AS dev_count,
        SUM(CASE WHEN dept = '인사팀' THEN 1 ELSE 0 END) AS hr_count
    FROM employees;
    ```
    
    - 조건에 맞으면 `1`, 아니면 `0`을 반환하게 하고 그걸 다 더하는(`SUM`) 방식
