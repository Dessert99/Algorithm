[https://school.programmers.co.kr/learn/courses/30/lessons/131116](https://school.programmers.co.kr/learn/courses/30/lessons/131116)
```sql
SELECT CATEGORY, PRICE AS MAX_PRICE, PRODUCT_NAME

FROM (SELECT CATEGORY, 
PRICE, 
PRODUCT_NAME,
-- 카테고리별로(PARTITION BY) 파티션을 나누고, 가격 내림차순(ORDER BY PRICE DESC)으로 순위를 매긴다.
RANK() OVER (PARTITION BY CATEGORY ORDER BY PRICE DESC) AS PR_RANK
FROM FOOD_PRODUCT
WHERE CATEGORY IN ('과자', '국', '김치', '식용유')
) AS FILTER

WHERE PR_RANK = 1

ORDER BY PRICE DESC
```
### 🔗 풀이
1. 🤔 식품분류별로 가격이 제일 비싼 식품 
    - CATEGORY별로 GROUP 했을 때, 가장 상위 물품.
    - GROUP BY하면 행이 사라진다!
    - 그래서 WHERE절에서 서브 쿼리를 만들어 카테고리 별 상위 물품을 뽑아야 한다.
### 🔗 배운점
1. 윈도우 함수: `RANK`
   ```sql
   RANK() OVER (PARTITION BY [그룹화할_기준_컬럼] ORDER BY [순위를_매길_기준_컬럼] [ASC|DESC])
   ```
   - `RANK()`
     - 순위를 매기는 윈도우 함수이다. 값이 같으면 공동 1등으로 처리하고, 다음 순위를 3등으로 건너뛴다.
   - `OVER (...)`
     - 어떤 범위의 창(Window)을 띄워놓고 순위를 매길 것인가?"를 정의한다.
   - `PARTITION BY`
     - `GROUP BY`와 비슷한 역할로, 데이터를 끼리끼리 묶어줄 '칸막이(그룹)'를 지정한다.
   - `ORDER BY`
     - 나누어진 칸막이 안에서, 어떤 컬럼을 기준으로 줄을 세울지(오름차순/내림차순) 정한다.
2. 윈도우 함수 Befor/After
   | **PRODUCT_ID** | **PRODUCT_NAME** | **PRODUCT_CD** | **CATEGORY** | **PRICE** |
    | --- | --- | --- | --- | --- |
    | P001 | 꿀꽈배기 | SN01 | 과자 | 1500 |
    | P002 | **홈런볼** | SN02 | **과자** | **1700** |
    | P003 | 미역국 | SP01 | 국 | 3000 |
    | P004 | **육개장** | SP02 | **국** | **4500** |
    | P005 | **종가집 김치** | KM01 | **김치** | **12000** |
    | P006 | 볶음김치 | KM02 | 김치 | 8000 |
    | P007 | **엑스트라 버진** | OL01 | **식용유** | **15000** |
    | P008 | 카놀라유 | OL02 | 식용유 | 9000 |

   | **CATEGORY** | **PRICE** | **PRODUCT_NAME** | **PR_RANK (RANK 윈도우 함수 결과)** |
    | --- | --- | --- | --- |
    | **과자** | **1700** | **홈런볼** | **1** *(과자 파티션 1등)* |
    | 과자 | 1500 | 꿀꽈배기 | 2 |
    | **국** | **4500** | **육개장** | **1** *(국 파티션 1등)* |
    | 국 | 3000 | 미역국 | 2 |
    | **김치** | **12000** | **종가집 김치** | **1** *(김치 파티션 1등)* |
    | 김치 | 8000 | 볶음김치 | 2 |
    | **식용유** | **15000** | **엑스트라 버진** | **1** *(식용유 파티션 1등)* |
    | 식용유 | 9000 | 카놀라유 | 2 |
