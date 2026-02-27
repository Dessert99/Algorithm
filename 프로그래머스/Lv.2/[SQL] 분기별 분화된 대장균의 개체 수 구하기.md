[https://school.programmers.co.kr/learn/courses/30/lessons/299308](https://school.programmers.co.kr/learn/courses/30/lessons/299308)
```sql
SELECT
    CONCAT_WS('', QUARTER(DIFFERENTIATION_DATE), 'Q') AS QUARTER, -- 분기 나눠주는 함수
    COUNT(ID) AS ECOLI_COUNT
FROM
    ECOLI_DATA
GROUP BY
    QUARTER
ORDER BY
    QUARTER
```
### 🔗 풀이

### 🔗 배운점
1. `QUARTER()` 정리
   > 입력된 날짜가 어느 분기에 속하는지 1, 2, 3, 4 중 하나의 숫자로 반환한다.
   
   | **월 (Month)** | **반환값 (Quarter)** | **비고** |
    | --- | --- | --- |
    | 1월, 2월, 3월 | **1** | 1사분기 (1Q) |
    | 4월, 5월, 6월 | **2** | 2사분기 (2Q) |
    | 7월, 8월, 9월 | **3** | 3사분기 (3Q) |
    | 10월, 11월, 12월 | **4** | 4사분기 (4Q) |
2. 예시 스니펫
   ```sql
      -- 2024년 5월 15일의 분기 구하기
      SELECT QUARTER('2024-05-15') AS q; 
      -- 결과: 2 (5월은 2분기)
   ```
3. 혹시 모르는 다른 함수 정리
    | **함수** | **설명** | **예시 (2024-05-15 기준)** |
    | --- | --- | --- |
    | **`YEAR()`** | 연도 추출 | `2024` |
    | **`MONTH()`** | 월 추출 (1~12) | `5` |
    | **`DAY()`** | 일 추출 (1~31) | `15` |
    | **`WEEK()`** | 해당 연도의 몇 번째 주인지 추출 | `20` (모드에 따라 다름) |
    | **`DAYOFWEEK()`** | 요일 번호 (1:일, 2:월 ... 7:토) | `4` (수요일) |
    | **`LAST_DAY()`** | 해당 월의 마지막 날짜 반환 | `2024-05-31` |
