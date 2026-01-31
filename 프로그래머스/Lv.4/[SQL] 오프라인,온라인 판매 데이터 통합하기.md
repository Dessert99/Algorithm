[https://school.programmers.co.kr/learn/courses/30/lessons/131537](https://school.programmers.co.kr/learn/courses/30/lessons/131537)
```sql
(
SELECT DATE_FORMAT(SALES_DATE, "%Y-%m-%d") AS SALES_DATE , PRODUCT_ID, USER_ID, SALES_AMOUNT

FROM ONLINE_SALE
    
WHERE SALES_DATE LIKE "%-03-%"
    
)

UNION

(
SELECT DATE_FORMAT(SALES_DATE, "%Y-%m-%d")AS SALES_DATE, PRODUCT_ID, NULL AS USER_ID, SALES_AMOUNT

FROM OFFLINE_SALE

WHERE SALES_DATE LIKE "%-03-%"

)

ORDER BY SALES_DATE, PRODUCT_ID, USER_ID
```
### 🔗 풀이
1. 조회 (SELECT) 
    - 판매 날짜 오프라인/(온라인)
    - 상품ID 오프라인/(온라인)
    - 유저ID오프라인/ (온라인)
        - `OFFLINE_SALE` 테이블의 판매 데이터의 `USER_ID` 값은 NULL 로 표시
    - 판매량 오프라인/(온라인)
2. 어디서 (FROM) 
    - `UNION ALL`
        - `OFFLINE_SALE`
        - `ONLINE_SALE`
3. 조건 (WHERE)
    - 2022년 3월
4. 정렬 (ORDER BY)
    - 판매일을 기준으로 오름차순 정렬
    - 판매일이 같다면 상품 ID를 기준으로 오름차순 정렬
    - 상품ID까지 같다면 유저 ID를 기준으로 오름차순 정렬
### 🔗 배운점
1. `JOIN` 과 `UNION` 구분
    
    > `JOIN` 은 수평 결합, `UNION`은 수직 결합
    > 
    - JOIN
        - A테이블에 B테이블의 컬럼을 붙이고 싶을 때 사용한다.
        - 데이터의 컬럼(너비)가 늘어난다.
    - UNION
        - A테이블의 행과 B테이블의 행을 하나의 리스트로 합칠 때 사용한다.
        - 데이터의 높이(행)이 늘어난다.
2. `UNION` 개념
    - 컬럼의 개수가 같아야 한다.
        
        ```sql
        ❌
        SELECT id, name FROM users             -- 2칸
        UNION
        SELECT id, name, email FROM admins;    -- 3칸 (ERROR!)
        
        -------
        
        ✅
        SELECT id, name, NULL AS email FROM users  -- 3칸 (억지로 맞춤)
        UNION
        SELECT id, name, email FROM admins;        -- 3칸
        ```
        
    - 컬럼의 순서와 데이터 타입이 호환되어야 한다.
        
        ```sql
        ❌
        SELECT age FROM users     -- 숫자 (25)
        UNION
        SELECT name FROM admins;  -- 문자 ('김철수') -> (ERROR! 형변환 실패)
        
        ---------
        
        ✅
        SELECT age FROM users
        UNION
        SELECT age FROM admins;   -- 숫자끼리 OK
        ```
        
    - 컬럼의 이름은 '첫 번째 SELECT 문'을 따라간다.
        
        ```sql
        -- 첫 번째 쿼리 (형님)
        ✅
        SELECT id AS '회원번호' FROM users
        
        UNION
        
        -- 두 번째 쿼리 (동생)
        -- ❌ 별칭 무시됨
        SELECT id AS '관리자ID' FROM admins;
        ```      
3. `UNION` 과 `UNION ALL`
    - `UNION` (중복 제거 O)
        - 두 테이블을 합친 뒤, 중복된 행이 있으면 하나만 남기고 지웁니다.
        - 중복을 검사해야 하므로 내부적으로 정렬을 수행합니다. -> **속도가 느림**
    - `UNION ALL` (중복 제거 X)
        - 그냥 묻지도 따지지도 않고 무조건 다 붙입니다.
        - 중복 검사가 없어서 속도가 훨씬 빠릅니다.
4. `UNION`을 써야한다는 판단 근거
   - `JOIN`은 서로 다른 정보를 옆으로 연결한다. 하지만 이 문제는 동일한 정보를 서로 다른 테이블에서 원하고 있다. 아래로 정보를 쌓아 한번에 보여줘야 한다.
   - `USER_ID` 값은 `NULL` 로 표시하라는 말은, 컬럼 개수를 맞추라는 의미이므로, `UNION`을 사용해야한다는 힌트이다.
6. 값이 없는 컬럼에 NULL 넣는 방법
    
    ```sql
     NULL AS USER_ID
     -- USER_ID 값에 NULL이 들어감
    ```
    
7. ⭐️ 인덱스 친화적 쿼리
    
    > 정렬되어 있다면 색인 접근이 효율적이다.
    > 
    
    ```sql
    ❌  YEAR(SALES_DATE) = 2022 AND MONTH(SALES_DATE) = 3
    ✅  SALES_DATE LIKE '2022-03%'
    ✅  
    WHERE SALES_DATE >= '2022-03-01'
      AND SALES_DATE < '2022-04-01'
    ```
