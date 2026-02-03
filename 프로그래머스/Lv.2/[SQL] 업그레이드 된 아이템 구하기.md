[https://school.programmers.co.kr/learn/courses/30/lessons/273711](https://school.programmers.co.kr/learn/courses/30/lessons/273711)
```sql
SELECT I.ITEM_ID, I.ITEM_NAME, I.RARITY

FROM ITEM_INFO AS I
    JOIN ITEM_TREE AS T
        ON I.ITEM_ID = T.ITEM_ID

-- 아이템의 희귀도가 'RARE'인 아이템들의 모든 다음 업그레이드 아이템
WHERE T.PARENT_ITEM_ID IN (
    SELECT ITEM_ID
    FROM ITEM_INFO
    WHERE RARITY = 'RARE'
)

ORDER BY ITEM_ID DESC
```
### 🔗 풀이
1. 🤔 JOIN을 해야 하는가?
    - ❌ 이 문제에서 조회해야하는 컬럼은 전부 ITEM_INFO에 있다. 굳이 JOIN할 필요는 없다.
        - 이 판단이 틀린 이유는 "조회는 하지 않지만 WHERE절에서 사용하기 때문"이다. JOIN은 필요하다.

2. 🤔 어떤 서브 쿼리 내용이 필요할까?
    - "아이템의 희귀도가 'RARE'인 아이템들"을 뽑아야 한다.

3. 🤔 서브 쿼리에 "ITEM_ID"를 조회해야 하는 이유?
    - ITEM_TREE 테이블은 ID만 알고 있기 때문이다.


### 🔗 배운점
1. 서브쿼리 
    
    > 쿼리 안의 쿼리이다. 조건이 다른 테이블에 있을 때 사용한다.
    > 
    
    ```sql
    SELECT 컬럼A, 컬럼B
    FROM 테이블1
    WHERE 검사할_컬럼 IN (
        SELECT 가져올_값      -- 여기가 서브쿼리!
        FROM 테이블2
        WHERE 조건
    );
    ```
    
    - 이 문제에서는 `PARENT_ITEM_ID`를 알고 싶은데, 조건(`RARITY`)이 `ITEM_ID` 테이블에 있다.
2. 서브쿼리 안에서는 테이블 원본 이름을 명시해야한다.
    
    ```sql
    ❌
    WHERE T.PARENT_ITEM_ID IN (
        SELECT I.ITEM_ID
        FROM I   -- ❌ 여기가 문제
        WHERE I.RARITY = 'RARE'
    )
    
    -----
    ✅
    SELECT ITEM_ID 
    FROM ITEM_INFO  -- I 대신 ITEM_INFO 사용
    WHERE RARITY = 'RARE'
    ```
