[https://school.programmers.co.kr/learn/courses/30/lessons/157339?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/157339?language=mysql)
```sql
SELECT C.CAR_ID, C.CAR_TYPE, (
     ROUND(30 * C.DAILY_FEE * (1 - IFNULL(P.DISCOUNT_RATE/100, 0)))
) AS FEE

FROM CAR_RENTAL_COMPANY_CAR AS C
LEFT JOIN CAR_RENTAL_COMPANY_DISCOUNT_PLAN AS P ON C.CAR_TYPE = P.CAR_TYPE
    AND P.DURATION_TYPE = '30일 이상' -- 30일 이상인 애들만 데려온다. 왼쪽 테이블은 살린다.

-- 조건1) 11월에 쓸 수 없는 자동차 CAR_ID
WHERE C.CAR_ID NOT IN (
            -- 11월에 대여 기록이 있는 자동차 목록
            SELECT CAR_ID
            FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
            WHERE START_DATE <= '2022-11-30' AND END_DATE >= '2022-11-01'
            )
-- 조건2) 세단, SUV 자동차
AND C.CAR_TYPE IN ('세단', 'SUV')

-- 조건3) 50만원 이상 200만원 미만
AND  ROUND(30 * C.DAILY_FEE * (1 - IFNULL(P.DISCOUNT_RATE/100, 0))) BETWEEN 500000 AND 1999999

ORDER BY FEE DESC, C.CAR_TYPE, C.CAR_ID DESC
```
### 🔗 풀이
1.  2022년 11월 1일부터 2022년 11월 30일까지 대여 가능
    - 일단 CAR_RENTAL_COMPANY_CAR의 CAR_ID는 전부 살아있어야 한다. 만약 HISTORY에 없는 CAR_ID는 무조건 대여 가능하기 때문이다.
    - 서브 쿼리로 만들까?
        - 👀 방법1)
            1. CAR_RENTAL_COMPANY_RENTAL_HISTORY테이블에서 11월에 불가능한 CAR_ID를 뽑는다.
            2. 그 ID와 매칭되지 않는 CAR_ID만 뽑는다.
### 🔗 배운점
1. 날짜 범위 다루기
    1. 두 기간이 겹치는가?
        
        > `OR` 말고 `AND`쓰자
        > 
        - **요구사항:** "2022년 11월에 단 하루라도 대여 중인 기록을 찾아라!"
            
            
            | **대여 시작 (START)** | **반납일 (END)** | **결과** | **이유** |
            | --- | --- | --- | --- |
            | 10-25 | 11-05 | ⭕ 포함 | 11월 앞부분에 걸침 |
            | 11-25 | 12-05 | ⭕ 포함 | 11월 뒷부분에 걸침 |
            | 10-25 | 12-05 | ⭕ 포함 | 🚨 **11월을 통째로 덮음 (OR 쓰면 놓치는 놈)** |
            
            ```sql
            -- 치트키: (내 시작일 <= 기준 끝일) AND (내 종료일 >= 기준 시작일)
            WHERE START_DATE <= '2022-11-30' 
              AND END_DATE >= '2022-11-01'
            ```
            
    2. 특정 달(Month)에 일어난 일인가?
        
        > 부등호보다 문자열로 예쁘게 잘라서 비교하자
        > 
        - **요구사항:** "2022년 11월에 '대여를 시작한' 기록을 찾아라!"
            
            
            | **원래 데이터 (DATETIME)** | **DATE_FORMAT 변환 후** | **결과** |
            | --- | --- | --- |
            | 2022-11-05 14:30:00 | **2022-11** | ⭕ 일치 |
            | 2022-12-01 09:00:00 | **2022-12** | ❌ 불일치 |
            
            ```sql
            -- 방법 A (가장 많이 씀): 형식을 'YYYY-MM'으로 강제 변환
            WHERE DATE_FORMAT(START_DATE, '%Y-%m') = '2022-11'
            
            -- 방법 B (꼼수): 문자열처럼 LIKE 쓰기
            WHERE START_DATE LIKE '2022-11-%'
            ```
            
    3. 며칠 동안 빌렸는가?
        
        > 실제 대여 기간을 구할 때는 반드시 `+ 1`을 해줘야 한다.
        > 
        - **요구사항:** "대여 기간이 30일 이상인 기록을 찾아라!"
            
            
            | **빌린 날** | **반납한 날** | **DATEDIFF 결과** | **실제 렌트 일수** |
            | --- | --- | --- | --- |
            | 11-01 | 11-01 | **0** (단순 뺄셈) | **1일** (당일치기 렌트) |
            | 11-01 | 11-02 | **1** | **2일** (1일, 2일 이틀간 렌트) |
            | 10-01 | 10-30 | **29** | **30일** (장기 렌트) |
            
            ```sql
            -- 🚨 틀린 코드: 당일치기를 0일로 계산해 버림
            WHERE DATEDIFF(END_DATE, START_DATE) >= 30 
            
            -- 🌟 100점 코드: 빌린 첫날도 1일로 쳐줘야 하므로 +1 필수!
            WHERE DATEDIFF(END_DATE, START_DATE) + 1 >= 30
            ```
            
    4. 단일 날짜가 특정 기간 안에 쏙 들어가는가? (BETWEEN)
        - **요구사항:** "가입일(JOIN_DATE)이 2022년 11월 한 달 안에 속하는 유저를 찾아라!"
            
            
            | **JOIN_DATE** | **BETWEEN 범위** | **결과** |
            | --- | --- | --- |
            | 2022-11-15 | 11-01 ~ 11-30 | ⭕ 포함 |
            | 2022-10-31 | 11-01 ~ 11-30 | ❌ 불포함 |
            
            ```sql
            -- A AND B 보다 훨씬 직관적이고 깔끔함
            WHERE JOIN_DATE BETWEEN '2022-11-01' AND '2022-11-30'
            ```
            
2. SELECT절에서 지은 별칭은 ORDER BY, HAVING 등에서 사용이 가능하다.
    
    ```sql
    ORDER BY FEE  DESC, -- 가능!
    ```
    
3. `IFNULL` 로직은 사실 쓸모 없었다.
    1. 할인이 없어도 `CAR_ID`를 살리기 위해 `LEFT JOIN` 을 사용했다.
    2. 하지만 `AND P.DURATION_TYPE = '30일 이상'` 로직에서 할인 정책이 없는 차들을 전부 버리게 된다.
    3. 사실상 `INNER JOIN` 과 동일하게 작동한다.
    4. 그럼에도 불구하고 정답인 이유
        - 애초에 데이터셋이 30일 이상 대여 시 무조건 할인율을 부여했기 떄문이다.
        - 실무에서나, 예외 조건이 조금 더 타이트한 문제에서는 오답이다.
4. 할인율이 없어도 테이블에서 살리는 방법
    
    > `JOIN ON` 에서 조건을 주가한다.
    > 
    
    ```sql
    SELECT C.차종, P.할인정책
    FROM 자동차 C
    LEFT JOIN 할인정책 P 
           ON C.차종 = P.차종 
          AND P.할인정책 = '30일 이상' -- (애초에 30일 정책 가진 애들만 데리고 합쳐!)
    ```
    
    - `ON`절의 조건은 "오른쪽 테이블(P) 데이터를 가져올지 말지 결정하는 기준"이다. `LEFT JOIN`과 함께 썼기 때문에 30일 이상이 아니라면 `NULL`값이 되지만 행이 사라지지는 않는다.
    - `WHERE` 절에 썼다면 `NULL` 인 행이 사라진다.
