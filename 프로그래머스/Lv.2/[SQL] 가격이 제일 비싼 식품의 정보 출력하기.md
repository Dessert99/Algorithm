[https://school.programmers.co.kr/learn/courses/30/lessons/131115?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/131115?language=mysql)
```sql
SELECT PRODUCT_ID, PRODUCT_NAME, PRODUCT_CD, CATEGORY, PRICE

FROM FOOD_PRODUCT

-- 가격이 제일 비싼 식품
WHERE PRICE = (
    SELECT MAX(PRICE)
    FROM FOOD_PRODUCT
)
```
### 🔗 풀이
1. 👀 방법1)
     1. 서브 쿼리로 가장 가격이 높은 행을 구한다.
     2. 이때 `Max()`문법을 사용한다.

### 🔗 배운점
1. `MAX`와 `NULL`의 관계
   > 만약 컬럼에 NULL 값이 있으면 어떻게 될까?
   - `MAX()` 함수는 `NULL` 값을 무시하고 계산한다.
   - 하지만 만약 모든 행이 `NULL`이라면? 결과는 `NULL`을 반환해. (0이 아님에 주의!)
2. `MAX`는 숫자만 다루지 않는다
   > 숫자 뿐만 아니라, 문자열과 날짜도 비교할 수 있다.
   - 날짜: 가장 최근(미래) 날짜 (예: 2023-01-01 vs 2024-01-01 -> 2024-01-01)
   - 문자열: 사전 순서상 가장 뒤에 있는 문자 (예: 'Apple' vs 'Banana' -> 'Banana')
        - 주의: 문자열 비교는 앞글자부터 아스키(ASCII) 코드 값 등으로 비교하기 때문에, '9'와 '10'을 비교하면 '9'가 더 크다고 판단할 수도 있다. (문자열 타입인 경우)
