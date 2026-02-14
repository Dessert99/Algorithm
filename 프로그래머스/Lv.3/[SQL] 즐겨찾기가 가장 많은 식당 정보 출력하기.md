[https://school.programmers.co.kr/learn/courses/30/lessons/131123?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/131123?language=mysql)
```sql
SELECT FOOD_TYPE, REST_ID,REST_NAME,  FAVORITES

FROM REST_INFO

-- 가장 많은 식당과 동일
WHERE (FOOD_TYPE, FAVORITES) IN (
    -- 음식종류별로 즐겨찾기수
        SELECT FOOD_TYPE, MAX(FAVORITES)
        FROM REST_INFO
        GROUP BY FOOD_TYPE
)

ORDER BY FOOD_TYPE DESC
```
### 🔗 풀이

### 🔗 배운점
1. `IN` 연산자의 비교 대상 일치
   - WHERE A IN (B) 구문을 쓸 때는, A와 B가 서로 같은 성질의 데이터여야 합니다.
   - 내가 했던 실수: WHERE FOOD_TYPE(문자) IN (SELECT COUNT(숫자))
   - 글자('한식')와 숫자(5)를 비교하려고 했기 때문에 논리적으로 성립하지 않았습니다.
   - 올바른 논리: WHERE 점수 IN (SELECT 1등점수)처럼 반드시 사과는 사과끼리, 숫자는 숫자끼리 비교해야 합니다.
2. 왜 다중 컬럼 서브쿼리으로 풀어야 할까?
   - 만약 `FAVORITES IN (SELECT MAX(FAVORITES) ... )` 으로 즐겨찾기 수로만 필터링 한다면, 그 즐겨찾기 수가 어떤 타입의 값인지 모른다.
3. `GROUP BY`에 적은 컬럼은 `SELECT`에 무조건 같이 적어주는 것이 좋다.
