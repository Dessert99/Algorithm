[https://school.programmers.co.kr/learn/courses/30/lessons/301650?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/301650?language=mysql)
```sql
WITH
    RECURSIVE TEMP
AS (
-- 1. 초기값 설정. 재귀의 시작점. 부모가 없는 애들을 찾아서 DEPTH = 1 로 만든다.
    SELECT
        ID,
        1 AS DEPTH
    FROM
        ECOLI_DATA
    WHERE
        PARENT_ID IS NULL
        
    UNION ALL
    
-- 2. 반복 연산. 자식 노드를 계속 찾아간다.
    SELECT
        CHILD.ID,
        DEPTH + 1
    FROM
        ECOLI_DATA AS CHILD
    JOIN
        TEMP AS PARENT 
      ON
        PARENT.ID = CHILD.PARENT_ID

-- 3. 종료 조건. 반복 연산의 종료 시점이다.
    WHERE
        DEPTH < 3 -- 3세대 까지
     )

SELECT
    T.ID AS ID
    
FROM
    TEMP AS T
    
WHERE
    T.DEPTH = 3 -- 3세대만 사용

ORDER BY
    T.ID
```
### 🔗 풀이
1. 🤔 3세대가 되려면
    1. PARENT_ID 가 NULL인 대장균들을 찾는다.
    2. 그 대장균들의 자식을 찾는다.
    3. 그 대장균들의 자식을 찾는다. -> 이것들이 3세대

2. 🤔 이걸 SQL로 해석하면?
    1. 부모, 자식 테이블을 셀프 조인한다. 이때, 부모 테이블 기준으로 조인한다.
    2. 여기에 다시 한 번 더 조인한다.
### 🔗 배운점
1. 계층형 재귀 구조 스니펫
   ```sql
    WITH
       RECURSIVE HierarchyCTE AS
    (
        -- 1. 초기값 설정: 최상위 노드(뿌리) 찾기 (예: 매니저가 없는 사장님)
        SELECT 
            ID, 
            PARENT_ID, 
            NAME, 
            1 AS DEPTH  -- 계층의 깊이를 나타내는 가상 컬럼 추가
        FROM 
            MY_TABLE
        WHERE 
            PARENT_ID IS NULL -- 최상위 조건
    
        UNION ALL
    
        -- 2. 반복 연산: 자식 노드 찾기 (원본 테이블과 CTE를 조인)
        SELECT 
            T.ID, 
            T.PARENT_ID, 
            T.NAME, 
            H.DEPTH + 1 -- 밑으로 내려갈수록 깊이 + 1
        FROM 
            MY_TABLE AS T
        INNER JOIN 
            HierarchyCTE AS H
        ON
            T.PARENT_ID = H.ID  -- 자식의 부모 ID(T.PARENT_ID) = 방금 찾은 노드의 ID(H.ID)
   
        -- 3. 종료 조건
        WHERE 
            H.DEPTH < 10 -- 안전장치
    )
    
    SELECT
           *
    FROM
       HierarchyCTE
    ORDER BY
       DEPTH; -- 계층 순서대로 정렬
   ```
2. 흐름 예시
   1. 원본 테이블
      | **ID** | **PARENT_ID** |
      | --- | --- |
      | **1** | NULL |
      | **2** | 1 |
      | **3** | 2 |
      | **4** | 3 |
   2. 실행 최초 1회
       | **TEMP.ID** | **TEMP.DEPTH** | **설명** |
       | --- | --- | --- |
       | **1** | **1** | 첫 번째 루프에서 찾은 1세대 대장균 |
      - `WHERE PARENT_ID IS NULL` 인 데이터를 찾고, `DEPTH`를 1로 설정하여 `TEMP`에 넣기
   4. 첫 번째 재귀
      | **TEMP.ID** | **TEMP.DEPTH** | **설명** |
      | --- | --- | --- |
      | 1 | 1 | (Step 1에서 누적됨) |
      | **2** | **2** | 두 번째 루프에서 찾은 2세대 대장균 |
      - 방금 찾은 `TEMP`의 데이터(`ID 1`)를 부모로 삼아 `ECOLI_DATA`와 `JOIN` 합니다. 1의 자식인 2를 찾고, `DEPTH`를 1+1=2로 만들어 추가한다.
   5. 두 번째 재귀
      | **TEMP.ID** | **TEMP.DEPTH** | **설명** |
      | --- | --- | --- |
      | 1 | 1 | (Step 1에서 누적됨) |
      | 2 | 2 | (Step 2에서 누적됨) |
      | **3** | **3** | 세 번째 루프에서 찾은 3세대 대장균 |
      - 방금 추가된 `TEMP`의 데이터(`ID 2`)를 부모로 삼아 또 `JOIN` 합니다. 2의 자식인 3을 찾고, `DEPTH`를 2+1=3으로 만들어 추가한다.
   6. 재귀 종료
      - 다음 재귀를 돌려고 보니, 우리가 재귀 쿼리 안에 작성해 둔 `WHERE DEPTH < 3` 조건에 걸립니다. 현재 `DEPTH`가 3이므로 더 이상 `JOIN`을 수행하지 않고 여기서 재귀 루프를 완전히 종료합니다. (만약 이 조건이 없었다면 4세대인 ID 4까지 탐색했을 겁니다.)
