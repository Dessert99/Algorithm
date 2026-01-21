[https://school.programmers.co.kr/learn/courses/30/lessons/131536?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/131536?language=mysql)
```sql

SELECT USER_ID, PRODUCT_ID

FROM ONLINE_SALE

GROUP BY USER_ID, PRODUCT_ID

HAVING COUNT(PRODUCT_ID)>=2

ORDER BY USER_ID, PRODUCT_ID DESC
```
### 🔗 풀이
1. 조회 (SELECT)
    - 재구매한 회원 ID
    - 재구매한 상품 ID
2. 조건 (WHERE)
    - 이 문제에서는 없다.
3. 그루핑 (GROUP BY)
    - 회원 ID, 상품 ID를 그루핑
4. 그룹 조건(HAVING)
    - 상품 ID 중복 이력 내역이 있는 회원 ( 상품 ID 개수가 2 이상인 행만 남긴다)
        
        ```sql
        HAVING COUNT(PRODUCT_ID)>=2
        
        // 이렇게 쓰기도 함
        HAVING COUNT(*) >= 2
        ```
        
5. 정렬 (ORDER BY)
    - 회원 ID를 기준으로 오름차순 정렬
    - 회원 ID가 같다면 상품 ID를 기준으로 내림차순 정렬

### 🔗 배운점
1. `동일한 날짜, 회원 ID, 상품 ID 조합에 대해서는 하나의 판매 데이터만 존재합니다.` 라는 조건 해석
    
    > 무결성
    > 
    - 만약 같은 날에 같은 상품을 여러 번 구매했어도, 테이블에는 하나의 행만 기록된다는 의미이다.
    - 만약 이 조건이 없으면 그루핑 했을 때, 중복 클릭으로 동일한 데이터가 여러 개 쌓이면 재구매처럼 보일 수도 있다.
2. `WHERE`와 `HAVING`의 차이와 내부 동작 원리
    
    > `WHERE`는 디스크에 있는 원본 데이터를 걸러내는 것이고, `HAVING`은 메모리에서 계산된 결과 데이터를 걸러내는 것이다.
    > 
    - `WHERE` 절은 데이터가 그룹핑되기 전에, 디스크에서 가져온 개별 행을 검사한다. 이때 인덱스를 활용해 데이터셋의 크기를 미리 줄여주므로 쿼리 성능 최적화에 결정적인 역할을 한다.
    - 반면, `HAVING` 절은 데이터가 그룹핑된 후에, 생성된 집계 결과를 필터링한다. 논리적으로 집계 함수는 그룹핑 이후에 연산되므로, `WHERE` 절에서는 사용할 수 없고 반드시 `HAVING` 절에서 처리해야 한다.
