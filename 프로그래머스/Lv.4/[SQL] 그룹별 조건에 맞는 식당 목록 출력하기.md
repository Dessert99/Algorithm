[https://school.programmers.co.kr/learn/courses/30/lessons/131124](https://school.programmers.co.kr/learn/courses/30/lessons/131124)
```sql
SELECT
  M.MEMBER_NAME,
  R.REVIEW_TEXT,
  DATE_FORMAT(R.REVIEW_DATE,'%Y-%m-%d') AS REVIEW_DATE -- 데이터 형식 맞추기
FROM
  MEMBER_PROFILE AS M
JOIN
  REST_REVIEW AS R
ON
  M.MEMBER_ID = R.MEMBER_ID

-- 서브 쿼리로 1등일 행만 뽑는다.
WHERE
   M.MEMBER_ID
IN (
    -- 여기에는 1등인 MEMBER_ID 목록을 출력한다.
    SELECT
      MEMBER_ID
    FROM (
        -- 여기에 등수 컬럼을 추가한 테이블을 넣는다.
        SELECT
          MEMBER_ID,
          RANK() OVER (ORDER BY COUNT(*) DESC ) AS RNK -- 이제 RNK 컬럼이 생기고 등수가 입력된다.
        FROM
           REST_REVIEW
        GROUP BY -- 회원 별로 리뷰 개수를 확인하기 위해서 그룹화한다.
          MEMBER_ID
    ) AS TMP
    WHERE
      RNK = 1 -- 새롭게 만들어진 테이블에서 1등인 행만 필터링
)
ORDER BY
    REVIEW_DATE, R.REVIEW_TEXT
```
### 🔗 풀이

### 🔗 배운점
1. 윈도우 함수는 `SELECT`, `ORDER BY`절에서만 쓸 수 있다.
2. `OVER ( )` 에서 괄호는 필수다.
3. `RANK`는 예약어라서 별칭으로 쓰면 안된다.
4. `GROUP BY` + `RANK` 흐름도
   1. 1단계: 원본 데이터 (`REST_REVIEW`)
      | **MEMBER_ID** | **REVIEW_TEXT** |
      | --- | --- |
      | **A** | 맛나요 |
      | **A** | 굿굿 |
      | **B** | 별로임 |
      | **B** | 쏘쏘 |
      | **C** | 최고 |
      | **A** | 추천 |
      | **B** | 가성비갑 |
   2. `GROUP BY MEMBER_ID` 실행 후
      | **MEMBER_ID** | **COUNT(*)** |
      | --- | --- |
      | **A** | **3** |
      | **B** | **3** |
      | **C** | **1** |
       - `GROUP BY`가 먼저 작동해서 데이터를 회원별로 압축하고, 개수(`COUNT`)를 셉니다.
   3. `RANK() OVER (ORDER BY COUNT(*) DESC)` 실행 후
      | **MEMBER_ID** | **COUNT(*)** | **RNK (등수)** |
      | --- | --- | --- |
      | **A** | 3 | **1** 🥇 |
      | **B** | 3 | **1** 🥇 |
      | **C** | 1 | **3** 🥉 |
      - 압축된 테이블에 새로운 컬럼이 생기고 등수가 매겨진다.
      - 이때 `RANK()` 함수 특성상 공동 1등이 2명이면, 2등은 건너뛰고 바로 3등이 된다.
    4. `WHERE RNK = 1` 실행 후 (최종 필터링)
       | **MEMBER_ID** | **RNK** |
        | --- | --- |
        | **A** | **1** |
        | **B** | **1** |
       - 이제 바깥쪽 WHERE 절에서 등수가 1인 사람만 남깁니다.
