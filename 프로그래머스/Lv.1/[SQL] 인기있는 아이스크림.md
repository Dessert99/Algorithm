[https://school.programmers.co.kr/learn/courses/30/lessons/133024?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/133024?language=mysql)
```sql
SELECT FLAVOR

FROM FIRST_HALF

ORDER BY TOTAL_ORDER DESC, SHIPMENT_ID
```
### 🔗 풀이
1. 타겟 SELECT
   - 상반기에 판매된 아이스크림의 맛
3. 정렬1
   - 총주문량을 기준으로 내림차순 정렬
3. 정렬2
   - 출하 번호를 기준으로 오름차순 정렬

### 🔗 배운점
1. 정렬 기준 설정
    ```sql
    SELECT *
    FROM 테이블명
    ORDER BY 컬럼명 DESC
    ```
    - **ASC**: 오름차순 (기본값, 생략 가능) — 작은 값 → 큰 값 (1, 2, 3...)
    - **DESC**: 내림차순 — 큰 값 → 작은 값 (10, 9, 8...)
2. 다중 정렬
    > 콤마(`,`)로 구분하여 나열합한다. 앞에 있는 컬럼이 최우선 기준이 된다.
    ```sql
    -- 부서(dept)는 오름차순으로 묶고, 같은 부서 내에서는 월급(salary)이 높은 순으로 정렬
    SELECT *
    FROM employees
    ORDER BY dept, salary DESC;
    ```
