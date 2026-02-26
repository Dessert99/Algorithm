[https://school.programmers.co.kr/learn/courses/30/lessons/301651](https://school.programmers.co.kr/learn/courses/30/lessons/301651)
```sql
WITH
    RECURSIVE GENERATION_TABLE AS (
    -- 1. 초기값
    SELECT
        ID,
        1 AS GENERATION
    FROM
        ECOLI_DATA
    WHERE
        PARENT_ID IS NULL
    
    UNION ALL
        
    -- 2. 반복 연산
    SELECT
        E.ID,
        GENERATION + 1
    FROM
        ECOLI_DATA AS E
    JOIN
        GENERATION_TABLE AS G -- 테이블을 가져와야 한다.
    ON
        E.PARENT_ID = G.ID
        
    -- 3. 종료 조건 -> 필요 없다. 왜지?
    )

SELECT 
    COUNT(ID) AS COUNT,
    GENERATION
    
FROM
    GENERATION_TABLE AS PARENT
    
WHERE
    NOT EXISTS (
        -- 자식이 없는 행
        SELECT
            1
        FROM
            ECOLI_DATA AS CHILD
        WHERE
            CHILD.PARENT_ID = PARENT.ID
    )
    
GROUP BY
    GENERATION
    
ORDER BY
    GENERATION
```
### 🔗 풀이
1. 🤔 각 세대별 자식이 없는 개체
    - `EXISTS` 연산자 사용하기
2. 🤔 수(`COUNT`)와 세대(`GENERATION`)
    - `GENERATION`는 새롭게 컬럼을 만들어야 할 것 같다.
3. 🤔 알아야 할 것들
    1. 계층 필요함. -> 재귀
    2. 자식이 없는가? -> `EXISTS`

### 🔗 배운점
1. `EXISTS` 연산자
   - 기본 스니펫
     ```sql
      SELECT
           *
      FROM
           A
      WHERE
           EXISTS (
              SELECT 1
              FROM B
              WHERE B.a_id = A.id
          -- AND 추가 조건들...
      );
     ```
     - 존재하지 않는지 확인하고 싶다면 `NOT EXISTS`를 써야 한다.
2. 다양한 예시
   1. "특정 기간에 주문한 유저”
      ```sql
        SELECT u.*
        FROM users u
        WHERE EXISTS (
          SELECT 1
          FROM orders o
          WHERE o.user_id = u.id
            AND o.created_at >= '2026-01-01'
            AND o.created_at <  '2026-02-01'
        );
      ```
   2. “주문 한 번도 없는 유저”
      ```sql
        SELECT u.*
        FROM users u
        WHERE NOT EXISTS (
          SELECT 1
          FROM orders o
          WHERE o.user_id = u.id
        );
      ```
   3. “부모 중 자식이 없는 leaf 찾기"
      ```sql
        SELECT p.id
        FROM ecoli_data p
        WHERE NOT EXISTS (
          SELECT 1
          FROM ecoli_data c
          WHERE c.parent_id = p.id
        );
      ```
3. `SELECT 1`의미
   > “아무 값이나 하나 반환해라”
   - 왜 이렇게 쓸까?
     - `EXISTS`는 서브쿼리의 `SELECT` 목록을 보지 않고, 행이 하나라도 반환되는지만 본다.
