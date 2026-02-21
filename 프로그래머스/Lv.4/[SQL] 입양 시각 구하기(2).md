[https://school.programmers.co.kr/learn/courses/30/lessons/59413?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/59413?language=mysql)
```sql
-- 가상 테이블 만들기 (재귀)
WITH
    RECURSIVE hour_table
AS
    (
        SELECT
            0 AS HOUR
        UNION ALL
        SELECT
            HOUR + 1
        FROM
            hour_table
        WHERE
            HOUR < 23 -- 23이 될 때까지 반복
    )

SELECT
    T.HOUR,
    COUNT(O.ANIMAL_ID) AS COUNT

FROM
    hour_table AS T
LEFT JOIN
    ANIMAL_OUTS AS O
ON -- 가상 테이블의 시간과 입양 테이블의 입양 시간을 조인한다.
    T.HOUR = HOUR(O.DATETIME)

GROUP BY
    T.HOUR

ORDER BY
    T.HOUR
```
### 🔗 풀이
1. 🤔 0시부터 23시까지, 각 시간대별
    - 시간 정보는 `DATETIME`에 있다. -> `HOUR()`
    - 중요한 점은, `HOUR`은 0부터 23까지 모든 행이 있어야 한다.
        - ??? 셀프 조인인가 -> 가상 테이블 만들고 `LEFT JOIN`한다.`

2. 🤔 입양이 몇 건이나 발생
    - 집계 함수
### 🔗 배운점
1. 요소 추출 함수 정리
   1. 날짜 요소 추출
      | **함수명** | **역할** | **BEFORE (입력)** | **AFTER (출력)** |
      | --- | --- | --- | --- |
      | **`YEAR(date)`** | 연도 추출 | `'2026-02-21 14:30:45'` | `2026` |
      | **`MONTH(date)`** | 월 추출 | `'2026-02-21 14:30:45'` | `2` |
      | **`DAY(date)`**
      (=`DAYOFMONTH`) | 일(Day) 추출 | `'2026-02-21 14:30:45'` | `21` |
      | **`QUARTER(date)`** | 분기 추출 (1~4) | `'2026-02-21 14:30:45'` | `1` (1분기) |
      | **`WEEK(date)`** | 일년 중 몇 번째 주인지 | `'2026-02-21 14:30:45'` | `7` (7주차) |
   2. 시간 요소 추출
      | **함수명** | **역할** | **BEFORE (입력)** | **AFTER (출력)** |
      | --- | --- | --- | --- |
      | **`HOUR(time)`** | 시(Hour) 추출 | `'2026-02-21 14:30:45'` | `14` |
      | **`MINUTE(time)`** | 분(Minute) 추출 | `'2026-02-21 14:30:45'` | `30` |
      | **`SECOND(time)`** | 초(Second) 추출 | `'2026-02-21 14:30:45'` | `45` |
   3. 요일 및 이름 추출
      | **함수명** | **역할** | **BEFORE (입력)** | **AFTER (출력)** | **특징** |
      | --- | --- | --- | --- | --- |
      | **`DAYOFWEEK(date)`** | 요일을 숫자로 | `'2026-02-21 14:30:45'` | `7` | **1=일요일**, 2=월요일... **7=토요일** |
      | **`DAYNAME(date)`** | 요일 영어 이름 | `'2026-02-21 14:30:45'` | `'Saturday'` | **문자열(String)** 반환 |
      | **`MONTHNAME(date)`** | 월 영어 이름 | `'2026-02-21 14:30:45'` | `'February'` | **문자열(String)** 반환 |
   4. 만능 추출 도구 : `EXTRACT()`
      > `EXTRACT(단위 FROM 날짜)`
      
      | **쿼리 예시** | **BEFORE (입력)** | **AFTER (출력)** | **설명** |
      | --- | --- | --- | --- |
      | `EXTRACT(YEAR FROM date)` | `'2026-02-21 14:30:45'` | `2026` | `YEAR()` 함수와 동일한 결과 |
      | `EXTRACT(MONTH FROM date)` | `'2026-02-21 14:30:45'` | `2` | `MONTH()` 함수와 동일한 결과 |
      | **`EXTRACT(YEAR_MONTH FROM date)`** | `'2026-02-21 14:30:45'` | **`202602`** | 연도와 월을 한 번에 **숫자**로 결합해서 추출 |
       - 사용법
         - 2026년 2월 데이터만 묶어서 보고 싶을 때, `YEAR(date) = 2026 AND MONTH(date) = 2` 라고 길게 쓰는 대신 `EXTRACT(YEAR_MONTH FROM date) = 202602` 라고 짧고 강력하게 조건을 걸 수 있습니다.
2. `WITH`절
    - CTE는 쿼리 내에서 일시적으로 생성되는 가상 테이블로, 쿼리 실행 동안만 존재한다.
    - 메인 쿼리에서 여러 번 참조하여 사용할 수 있다.
    
    ```sql
    WITH WithTable AS (
        SELECT column1, column2
        FROM table_1
        WHERE condition
    )
    SELECT *
    FROM WithTable
    WHERE column1 = '홍길동';
    ```
    
3. `WITH RECURSIVE` 문 (재귀 쿼리)
    - 포인트: `UNION`사용하기
    
    ```sql
    WITH
    	RECURSIVE TIME_TABLE
    AS 
    	(
        SELECT -- 재귀 초깃값
    	    0 AS HOUR            -- 1. 초기값 0을 세팅
        UNION ALL
        
        SELECT -- 재귀
    	    HOUR + 1             -- 2. 기존 HOUR 값에 1을 더함
        FROM
    	    TIME_TABLE 
        WHERE -- 재귀 정지 조건
    	    HOUR < 23             -- 3. HOUR이 23이 될 때까지 반복
    	)
    SELECT
    	*
    FROM
    	TIME_TABLE;
    ```
