[https://school.programmers.co.kr/learn/courses/30/lessons/298519?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/298519?language=mysql)
```sql
SELECT
    COUNT(ID) AS FISH_COUNT,
    MAX(LENGTH) AS MAX_LENGTH,
    FISH_TYPE

FROM
    FISH_INFO
    
GROUP BY
    FISH_TYPE

HAVING
    AVG(IFNULL(LENGTH, 10)) >= 33 -- NULL값 처리를 해줘야 한다.
    
ORDER BY
    FISH_TYPE
```
### 🔗 풀이
1. 🤔 평균 길이가 33cm 이상인 물고기들을 종류별로 분류
    - 타입 별로 그룹
    - 길이 평균을 구한다.
    - HAVING으로 33이상인지 판단.
2. 🤔 10cm이하의 물고기들은 10cm로 취급
    - LENGTH가 NULL이면 10cm로 취급한다.
### 🔗 배운점
1. `IFNULL()` 위치
   - 어디든 들어갈 수 있다.
