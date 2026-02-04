[https://school.programmers.co.kr/learn/courses/30/lessons/293261?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/293261?language=mysql)
```sql
SELECT I.ID, N.FISH_NAME, I.LENGTH

FROM FISH_INFO AS I
    JOIN FISH_NAME_INFO AS N
        ON I.FISH_TYPE = N.FISH_TYPE

WHERE (I.FISH_TYPE, I.LENGTH) IN (
        SELECT FISH_TYPE, MAX(LENGTH)
        FROM FISH_INFO 
        GROUP BY FISH_TYPE
)
ORDER BY I.ID
```
### 🔗 풀이
1. 💡 잡은 물고기의 길이가 10cm 이하일 경우
    - LENGTH === NULL

2. 🤔 GROUP BY 써야 할까?
    - "물고기 종류 별로" 라는 워딩이 보인다. FISH_TYPE 기준으로 그루핑이 필요하다.

3. 🤔 JOIN 필요할까?
    - 조회해야 하는 정보가 두 테이블에 나뉘어져 있다. FISH_TYPE 기준으로 JOIN하자.

### 🔗 배운점
1. 이 문제를 `GROUP BY` 만으로 풀지 못하는 이유
    
    > `GROUP BY`는 데이터를 그룹으로 압축하여 개별 정보는 보지 못한다.
    > 
    - 그룹화 하는 순간 각 행의 고유한 식별자는 의미를 잃는다. 최대 길이를 알아도, 그게 누구 `ID`인지 모른다.
    - 어떤 행인지 특정하기 위해서는 별도의 필터링이 필요하다.
2. `WHERE IN` 의미
    
    > 똑같은 행을 찾아라.
    > 
    
    ```sql
    WHERE (FISH_TYPE, LENGTH) IN (
        SELECT FISH_TYPE, MAX(LENGTH) 
        FROM FISH_INFO 
        GROUP BY FISH_TYPE
    )
    ```
    
    - `FISH_TYPE, LENGTH` 과 동일한 행을 서브 쿼리에서 찾아라.
3. `WHERE` 절에서는 `MAX`, `SUM` 같은 집계 함수를 쓸 수 없다.
    
    > `WHERE` 절은 데이터를 **가져오는** 단계이고, `MAX` 같은 집계 함수는 데이터를 다 가져온 후 계산하는 단계
    > 
    
    ```sql
    WHERE MAX(LENGTH) = ... ❌
    
    WHERE (컬럼A, 컬럼B) IN (...) ⭕
    ```
    
    - 집계 함수는 데이터를 메모리에서 전부 가져온 뒤에 실행할 수 있다.
    - `WHERE`절은 메모리에서 가져올 때 데이터를 필터링하는 용도이다.
    - 이렇게 둘의 실행 시점이 다르기 떄문에 `WHERE` 절과 집계 함수를 함께 사용할 수 없다.
