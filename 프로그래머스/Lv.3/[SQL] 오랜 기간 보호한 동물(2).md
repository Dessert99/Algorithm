[https://school.programmers.co.kr/learn/courses/30/lessons/59411?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/59411?language=mysql)
```sql
SELECT 
    O.ANIMAL_ID,
    O.NAME

FROM
    ANIMAL_INS AS I
JOIN
    ANIMAL_OUTS AS O
ON
    I.ANIMAL_ID = O.ANIMAL_ID

ORDER BY
    -- 보호 기간이 가장 길었던 순으로 정렬
    TIMESTAMPDIFF(SECOND, I.DATETIME, O.DATETIME) DESC

LIMIT 2
```
### 🔗 풀이

### 🔗 배운점
1. `TIMESTAMPDIFF(unit, start, end)`
   ```sql
          -- 시간(HOUR) 단위 차이 계산
      SELECT TIMESTAMPDIFF(HOUR, '2023-10-01 10:00:00', '2023-10-01 14:30:00') AS HOUR_DIFF; // -> 4
   ```
   - 시간 차이는 4시간 30분이지만 단위가 `HOUR`이기 떄문에 4가 출력된다.
2. `unit` (단위) 종류 정리
   | **단위 (unit)** | **설명** |
    | --- | --- |
    | `MICROSECOND` | 마이크로초 단위 차이 |
    | `SECOND` | 초 단위 차이 |
    | `MINUTE` | 분 단위 차이 |
    | `HOUR` | 시간 단위 차이 |
    | `DAY` | 일 단위 차이 |
    | `WEEK` | 주 단위 차이 |
    | `MONTH` | 월 단위 차이 |
    | `QUARTER` | 분기 단위 차이 |
    | `YEAR` | 연도 단위 차이 |
3. `DATEDIFF()` 와 차이점
   - 시작, 끝 위치가 반대이다.
   - `DATEDIFF()`는 무조건 날짜 차이만 계산할 수 있다.
