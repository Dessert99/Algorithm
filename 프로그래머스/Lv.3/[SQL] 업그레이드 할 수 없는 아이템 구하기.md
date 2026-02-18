[https://school.programmers.co.kr/learn/courses/30/lessons/273712?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/273712?language=mysql)
```sql
SELECT I.ITEM_ID, I.ITEM_NAME, I.RARITY

FROM ITEM_INFO AS I
JOIN ITEM_TREE AS T ON I.ITEM_ID = T.ITEM_ID

-- ITEM_TREE 테이블에서 PARENT_ITEM_ID에 없는 ITEM_ID만 출력
WHERE I.ITEM_ID NOT IN (
    -- PARENT_ITEM_ID 목록
    SELECT PARENT_ITEM_ID
    FROM ITEM_TREE
    WHERE PARENT_ITEM_ID IS NOT NULL
)

ORDER BY I.ITEM_ID DESC
```
### 🔗 풀이
1. 💡 더 이상 업그레이드할 수 없는 아이템
    - PARENT_ITEM_ID에 ITEM_ID가 없는 아이템

2. 🤔 일단 ITEM_TREE 테이블에 모든 ITEM_ID가 있다.
    - 이 테이블만으로 더 이상 업그레이드할 수 없는 아이템을 구분할 수 있다.
### 🔗 배운점
1. `NOT IN` 사용 주의사항 - `NULL`값이 있으면 안된다.
   > SQL에게 `NULL`은 '빈 칸'이 아니라 '모르는 값'이다.
    - `5 != NULL` 코드에 대한 sql는 UNKNOWN을 반환한다.
       ```sql
         WHERE 아이템ID NOT IN (10, 20, NULL)
       ```
    - 명령어를 DB는 다음과 같이 읽는다.
       ```
        "아이템ID가 10도 아니어야 하고(AND),
        20도 아니어야 하고(AND),
        NULL도 아니어야 한다."
       ```
     - 수식으로 쓰면 이렇게 된다.
       ```sql
        아이템ID != 10
        AND
        아이템ID != 20
        AND
        아이템ID != NULL  <-- 여기가 문제의 폭탄
       ```
     - 이제 결과는 `TRUE AND TRUE AND UNKNOWN = UNKNOWN` 이렇게 된다. 하지만 `AND`연산은 하나라도 `UNKNOWN`이 있다면 TRUE를 반환하지 않는다.
     - `WHERE` 절은 조건의 결과가 오직 TRUE인 행만 보여주기 때문에 아무런 행도 가져오지 않는다.
2. 해결 방법
   > 서브쿼리에서 `WHERE PARENT_ITEM_ID IS NOT NULL` 코드를 넣어서 `NULL`값을 사전에 제거해야 한다.
   - 이렇게 되면 `NOT IN`연산에 방해되는 `NULL`값이 없어 원하는 결과를 얻게 된다.
3. ⭐️ 정석 풀이법 (`LEFT JOIN`)
   ```sql
      SELECT I.ITEM_ID, I.ITEM_NAME, I.RARITY
      
      FROM ITEM_INFO AS I
      LEFT JOIN ITEM_TREE AS T ON I.ITEM_ID = T.PARENT_ITEM_ID
      -- 이렇게 LEFT JOIN하면 테이블 구조가 어떻게 될까? -> 
      
      WHERE T.ITEM_ID IS NULL -- "자식이 없는(NULL) 경우만 남기기
      
      ORDER BY I.ITEM_ID DESC
   ```
      | **ITEM_ID** | **ITEM_NAME** | **RARITY** |
      | --- | --- | --- |
      | **1** | 기본검 | COMMON |
      | **2** | 강화검 | RARE |
      | **3** | 전설검 | LEGEND |
      
      | **ITEM_ID (자식)** | **PARENT_ITEM_ID (부모)** |
      | --- | --- |
      | 2 | **1** |
      | 3 | **2** |
      
      | **I.ITEM_ID** | **I.ITEM_NAME** | **I.RARITY** | **T.ITEM_ID (자식)** | **T.PARENT_ITEM_ID** | **상태 해석** |
      | --- | --- | --- | --- | --- | --- |
      | 1 | 기본검 | COMMON | 2 | 1 | "난 자식(2번)이 있어" |
      | 2 | 강화검 | RARE | 3 | 2 | "난 자식(3번)이 있어" |
      | **3** | **전설검** | **LEGEND** | **NULL** | **NULL** | **"난 자식이 없어!"** |
