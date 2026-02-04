[https://school.programmers.co.kr/learn/courses/30/lessons/59408?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/59408?language=mysql)
```sql
SELECT COUNT(DISTINCT NAME)
FROM ANIMAL_INS
```
### 🔗 풀이

### 🔗 배운점
1. `DISTINCT` 핵심 정리
   > `DISTINCT`는 `SELECT`된 결과 집합에서 중복된 행을 제거하고 한 번만 보여주는 명령어이다.
   - `DISTINCT`는 나열된 컬럼 전체의 조합을 기준으로 중복을 판단한다.
     ```sql
        SELECT DISTINCT NAME, ANIMAL_ID
        FROM ANIMAL_INS;
     ```
     - (`NAME + ANIMAL_ID`) 쌍이 완전히 똑같은 경우에만 중복으로 쳐서 제거한다.
  - `NULL`과의 관계
     - `DISTINCT`는 `NULL`도 하나의 값으로 취급한다. 만약 `NULL`이 여러 개라면, 중복을 제거하고 딱 하나의 `NULL`만 남겨준다.
2. 중복을 제거하는 3가지 방법
  1. `DISTINCT`
      ```sql
        -- 동물의 이름 종류만 알고 싶다.
          SELECT DISTINCT NAME
          FROM ANIMAL_INS;
      ```
     - 단순히 "어떤 종류가 있는지" 리스트만 필요할 때 사용
  3. `GROUP BY`
     ```sql
        -- 이름별로 중복을 제거하고, 그 이름으로 몇 마리가 들어왔는지도 알고 싶다.
        SELECT NAME, COUNT(*)
        FROM ANIMAL_INS
        GROUP BY NAME;
     ```
     - 중복을 제거한 뒤에 "그래서 걔가 몇 개인데?", "그중 가장 큰 값은 뭔데?" 같은 추가 계산이 필요할 때 사용
