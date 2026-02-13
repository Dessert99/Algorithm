[https://school.programmers.co.kr/learn/courses/30/lessons/157340](https://school.programmers.co.kr/learn/courses/30/lessons/157340)
```sql
SELECT CAR_ID,
-- 3. MAX() 결과가 1이냐에 따라 대여중, 대여 가능을 표시한다.
CASE 
    WHEN  -- 2. 그 다음 [0, 0, 1...] 이런 정보에서 가장 큰 값 1을 확인한다.
        MAX(CASE
           -- 1.우선 CAR_ID 옆에 마킹하기
            WHEN START_DATE <= '2022-10-16' AND END_DATE >= '2022-10-16' THEN 1
            ELSE 0
          END) = 1 -- 2022-10-16 대여중인 마킹이 있는지 확인
    THEN '대여중'
    ELSE '대여 가능'
END AS AVAILABILITY
    

FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY

GROUP BY CAR_ID

ORDER BY CAR_ID DESC
```
### 🔗 풀이
1. 🤔 원래 테이블에 없는 컬럼이 출력된다 -> `SELECT`에서 `CASE THEN`

2. 🤔 `CAR_ID`는 고유값이 아니다. -> 한 자동차가 여러 번 대여되었다 -> `GROUP BY CAR_ID` 근거

3. 🤔 `SELECT`절에서 단순히 2022-10-16이 포함되는지 안되는지만 확인한다면?
    1. `START_DATE` ~ `END_DATE` 사이에 2022-10-16이 포함되는 `CAR_ID`를 확인한다.
    2. 포함되었다면 대여중, 포함되지 않았다면 대여 가능을 표시한다.
    3. `CAR_ID`는 여러 개 이고, 이전 기록에는 대여 가능이 표시되고, 최근 기록에는 대여중이 표시된다.
    4. 이러면 어떤 `CAR_ID`를 써야할지 기준이 모오하다.

5. 👀 방법1) -> ❌
    1. `START_DATE` ~ `END_DATE` 사이에 2022-10-16이 있는지 확인하고, `CAR_ID`에 대여가능, 대여중을 표시한다.
    2. `CAR_ID`별로 그루핑한다. 그리고 `HISTORY_ID`가 가장 높은 차의 대여중, 대여가능 표시를 출력한다.
    - 🤔 안되는 이유?
        - 예약을 미리 해놓는다면, `HISTROY_ID`가 높지만, 대여 가능으로 뜨게 된다.
        - `HISTORY_ID` 기준으로 `MAX`하면 현재 대여중인지 파악할 수 없다.

6. 👀 방법2) -> ✅
    1. 일단 `START_DATE` ~ `END_DATE` 사이에 2022-10-16이 있다면 `CAR_ID`옆에 1이라는 마킹을 한다.
    2. `CAR_ID` 기준으로 `GROUP BY`한다.
    3. 그 `CAR_ID` 안에 1이 하나라도 있다면 대여중이다.
    4. 만약 `CAR_ID` 묶음에 0만 있다면 대여 가능이다.

### 🔗 배운점
1. `WHERE`절이 아니라, `SELECT`절에서 구현해야하는 이유
    - 우리는 `CAR_ID`를 가져와서, `START_DATE`, `END_DATE`에 따라 표시를 다르게 해야 한다. 만약 `WHERE`절에서 이 로직을 사용하면, 조건을 불만족하는 행이 사라지기 때문에 `SELECT`절에서 써야 한다.
2. 왜 우리는 `CASE THEN`을 두 번 써야 하는가?
   - 일단 `CAR_ID`가 2022-10-16에 대여된 기록이 있는지 마킹해놓는 로직이 필요하고, 그 로직에 따라 대여중, 대여 가능을 표시하기 위해서이다.
3. 다른 방법: 서브쿼리
   ```sql
      SELECT CAR_ID,
             CASE 
                 -- 2단계: 이 차가 '범인 명단' 안에 들어 있는가?
                 WHEN CAR_ID IN (
                     -- 1단계: 10월 16일에 대여 중인 차들의 ID만 뽑아낸 '범인 명단' (서브쿼리)
                     SELECT CAR_ID
                     FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
                     WHERE '2022-10-16' BETWEEN START_DATE AND END_DATE
                 ) THEN '대여중'
                 ELSE '대여 가능'
             END AS AVAILABILITY
      FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
      GROUP BY CAR_ID
      ORDER BY CAR_ID DESC;
   ```
