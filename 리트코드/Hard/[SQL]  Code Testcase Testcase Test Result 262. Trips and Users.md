[https://leetcode.com/problems/trips-and-users/description/](https://leetcode.com/problems/trips-and-users/description/)
```sql
SELECT
    T.request_at AS Day,
    ROUND(
        -- 취소율
       ( SUM(T.status != 'completed')
         / COUNT(T.id)), 2) AS 'Cancellation Rate'

FROM
    Trips AS T
JOIN
    Users AS CLIENT
ON
    T.client_id = CLIENT.users_id
JOIN
    Users AS DRIVER
ON
    T.driver_id = DRIVER.users_id

-- 조건1. 2013-10-01 ~ 2013-10-03 사이
WHERE
    T.request_at BETWEEN '2013-10-01' AND '2013-10-03' AND
-- 조건2. 둘다 차단되지 않아야 함.
    CLIENT.banned = 'NO' AND DRIVER.banned = 'NO'

-- 그룹화 - 날짜
GROUP BY
    T.request_at -- varchar 타입이다.

ORDER BY
    T.request_at
```
### 🔗 풀이
1. 💡 취소율
    - 해당 일에 차단되지 않은 사용자가 취소한(클라이언트 또는 드라이버에 의해 취소된) 요청 수
        나누기
        1. status = completed인 행들
    - 해당 날짜에 차단되지 않은(unbanned) 사용자의 전체 요청 수 
        
2. 🤔 2013-10-01부터 2013-10-03 사이의 각 날짜 별로
    1. 일단 2013-10-01 ~ 2013-10-03 에 해당되는 행만 Trips에서 뽑는다.
    2. 날짜 기준 GROUP BY

3. 🤔 취소율은 소수점 둘째 자리까지 반올림하여 표시
    - ROUND(num, 2)

4. 💡 문제 파악
    1. 일단 차단되지 않은 사용자이어야 함.
        - banned = 'NO'
    2. 클라이언트, 드라이버 모두
### 🔗 배운점
1. '2013-10-01' 문자열에 `BETWEEN`이 작동하는 이유
   - SQL에서 문자열(`VARCHAR`)을 비교할 때는 사전식 순서를 따른다.
   - 문자열을 왼쪽부터 한 글자씩 비교하여 ASCII 코드 값이 큰 쪽을 '크다'고 판단한다.
   - 날짜가 YYYY-MM-DD 형식으로 저장되어 있다면, 가장 큰 단위인 연도(Year)부터 월(Month), 일(Day) 순서로 배치된다. 예: '2013-10-01' vs '2013-10-02'
      - 앞의 9글자(2013-10-0)는 같지만, 마지막 글자가 2 > 1이므로 '2013-10-02'가 더 큰 값으로 인식된다.
   - 문자열이라도 날짜 형식을 잘 지키고 있다면 `BETWEEN`, `>`, `<`, `ORDER BY` 모두 숫자를 비교하듯 정확하게 작동한다.
2. `SUM()` 안에 조건문을 쓰는 방법: 조건부 집계 (MySQL 기준)
   - MySQL에서 `status != 'completed'`와 같은 비교 연산의 결과는 불리언 값인 `TRUE` 또는 `FALSE`이다.
      - `TRUE`는 숫자 1로 취급된다.
      - `FALSE`는 숫자 0로 취급된다.
   - 표준 SQL방식
     ```sql
        SUM(CASE WHEN status != 'completed' THEN 1 ELSE 0 END)
     ```
     - 이렇게 풀어도 된다.
