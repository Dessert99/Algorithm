[https://school.programmers.co.kr/learn/courses/30/lessons/299305?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/299305?language=mysql)
```sql
SELECT Parent.ID, COUNT(Child.ID) AS CHILD_COUNT

FROM ECOLI_DATA AS Parent
   LEFT JOIN ECOLI_DATA AS Child  -- 자식이 없는 ID도 살리기 위해서 LEFT JOIN
        ON Parent.ID = Child.PARENT_ID

GROUP BY Parent.ID -- 부모 ID별로 딸린 자식 ID를 세야 한다.

ORDER BY Parent.ID
```
### 🔗 풀이
1. 조회 (SELECT)
    - 개체의 ID(`ID`)
    - 자식의 수(`CHILD_COUNT`)
        - 자식이 없다면 자식의 수는 0으로 출력
2. 테이블 (FROM)
    - 셀프 조인
        - 부모와 자식 관계가 하나의 테이블 안에 존재하므로 셀프 조인을 사용한다.
        - 조인 방식 (LEFT JOIN)
            - 일반 `JOIN`은 `INNER JOIN이`기 때문에, 자식이 없는 부모 ID는 사라진다. 우리는 0으로 출력해야하기 때문에`LEFT JOIN`을 해줘서 행을 살려야한다.
            
            ```sql
            FROM ECOLI_DATA AS Parent
              ❌  JOIN ECOLI_DATA AS Child
                    ON Parent.ID = Child.PARENT_ID
            ```
            
            ```sql
            FROM ECOLI_DATA AS Parent
              ✅  LEFT JOIN ECOLI_DATA AS Child
                    ON Parent.ID = Child.PARENT_ID
            ```
            
        - 연결 고리 (ON)
            
            ```sql
            Parent.ID = Child.PARENT_ID
            ```
            
3. 그룹 (GROUP BY)
    - 부모 ID별로 여러 자식 ID가 있고, 세야하기 때문에 그룹화가 필요하다.
4. 정렬 (ORDER BY)
    - ID
### 🔗 배운점
1. 셀프 조인
    
    > 하나의 테이블 내에 있는 컬럼들끼리 관계(상하 관계, 연관 관계 등)가 있을 때, **마치 서로 다른 두 테이블인 것처럼** 조인하는 기법
    > 
    - 반드시 `AS`를 사용하여 서로 다른 별칭을 지정해야 한다.
2. LEFT JOIN을 해야하는 이유
    
    > 왼쪽 테이블의 모든 행’과 ‘오른쪽 테이블에서 왼쪽 테이블과 공통된 값’을 가지고 있는 행들을 반환한다.
    만약 오른쪽 테이블에서 공통된 값을 가지고 있는 행이 없다면 NULL 값을 반환한다.
    > 
    
    <img width="508" height="406" alt="image" src="https://github.com/user-attachments/assets/ad5cc240-0c21-4a1f-8e91-d87925e1c297" />

    
3. NULL값에 COUNT 집계 함수를 쓴다면?
    - `COUNT(Child.ID)`를 사용하면, 자식이 없어서 `Child.ID`가 `NULL`인 경우 자동으로 0으로 계산된다. 별도의 `IFNULL` 처리가 필요 없다.
