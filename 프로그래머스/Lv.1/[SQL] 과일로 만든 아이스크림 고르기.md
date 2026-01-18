[https://school.programmers.co.kr/learn/courses/30/lessons/133025?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/133025?language=mysql)
```sql
SELECT FIRST_HALF.FLAVOR

FROM FIRST_HALF JOIN ICECREAM_INFO ON FIRST_HALF.FLAVOR = ICECREAM_INFO.FLAVOR

WHERE FIRST_HALF.TOTAL_ORDER > 3000 AND ICECREAM_INFO.INGREDIENT_TYPE = 'fruit_based'

ORDER BY FIRST_HALF.TOTAL_ORDER DESC
```
### 🔗 풀이
1. 타깃 (SELECT)
    - 아이스크림의 맛
2. 조건 (WHERE)
    - 상반기 아이스크림 총주문량이 3,000보다 높음
    - 아이스크림의 주 성분이 과일
3. 정렬 (ORDER BY)
    - 총주문량이 큰 순서

### 🔗 배운점
1. JOIN
    - 기본 문법
        
        ```sql
        SELECT A.컬럼1, B.컬럼2       -- 4. 누구의 컬럼인지 명시 (가장 중요)
        FROM 테이블A AS A            -- 1. 기준 테이블 (별칭 A)
        JOIN 테이블B AS B            -- 2. 붙일 테이블 (별칭 B)
        ON A.연결고리 = B.연결고리     -- 3. 두 테이블을 잇는 공통 값(Key)
        WHERE A.조건 = '값';         -- 5. 필요한 데이터만 필터링
        ```
        
    - 언제 써야 할까?
        - 데이터가 **여러 테이블에 흩어져 있을 때**
