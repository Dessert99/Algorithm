[https://school.programmers.co.kr/learn/courses/30/lessons/59042?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/59042?language=mysql)
```sql
SELECT O.ANIMAL_ID, O.NAME

FROM ANIMAL_OUTS AS O LEFT JOIN ANIMAL_INS AS I ON O.ANIMAL_ID = I.ANIMAL_ID

-- 여기서 INTAKE_CONDITION가 없는 행만
WHERE I.INTAKE_CONDITION IS NULL

ORDER BY O.ANIMAL_ID
```
### 🔗 풀이

### 🔗 배운점
1. INNER JOIN을 하면 안되는 이유
    1. 그냥 JOIN하면 두 테이블 모두 NAME이 존재하는 행만 살아남는다.
    2. LEFT JOIN해서 ANIMAL_OUTS테이블의 NAME은 모두 살린다.
    3. ANIMAL_INS에 없는 NAME의 다른 ANIMAL_INS 행들은 NULL값으로 채워진다.
    4. 우리는 그 NULL값인 행을 찾아 NAME을 가져오면 된다.
2. `LEFT JOIN` 병합 과정
    1. 조인하기 전 (두 개의 독립된 테이블)
        
        
        ANIMAL_OUTS
        
        | **ANIMAL_ID** | **NAME** |
        | --- | --- |
        | A1 | 초코 |
        | A2 | 밀크 |
        | A3 | 라떼 |
        
        ANIMAL_INS
        
        | **ANIMAL_ID** | **NAME** |
        | --- | --- |
        | A1 | 초코 |
        | A2 | 밀크 |
    2. LEFT JOIN 실행
        
        ```sql
        LEFT JOIN ANIMAL_INS AS I ON O.ANIMAL_ID = I.ANIMAL_ID
        ```
        
        - 이 명령어를 실행하는 순간 메모리 상에는 아래처럼 두 테이블의 컬럼이 가로로 나란히 이어 붙은 거대한 임시 표가 만들어진다.
            
            
            | **O.ANIMAL_ID** | **O.NAME** | **I.ANIMAL_ID** | **I.NAME** |
            | --- | --- | --- | --- |
            | A1 | 초코 | A1 | 초코 |
            | A2 | 밀크 | A2 | 밀크 |
            | A3 | 라떼 | NULL | NULL |
    3. WHERE 조건 필터링
        
        ```sql
        WHERE I.ANIMAL_ID IS NULL
        ```
        
        | **O.ANIMAL_ID** | **O.NAME** | **I.ANIMAL_ID** | **I.NAME** |
        | --- | --- | --- | --- |
        | A3 | 라떼 | NULL | NULL |
    4. SELECT
        
        ```sql
        SELECT I.ANIMAL_ID, I.NAME
        ```
        
        - NULL, NULL이 출력
        
        ```sql
        SELECT O.ANIMAL_ID, O.NAME
        ```
        
        - A3, 라떼가 출력
