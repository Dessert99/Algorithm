[https://school.programmers.co.kr/learn/courses/30/lessons/164670?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/164670?language=mysql)
```sql
SELECT
    USER_ID,
    NICKNAME,
    (
        -- 시 + 도로명 주소 + 상세 주소
        -- CONCAT_WS는 구분자를 기준으로 문자열을 합쳐준다. 이때, NULL은 자동으로 제외한다.
        CONCAT_WS(' ', CITY, STREET_ADDRESS1, STREET_ADDRESS2)
    ) AS 전체주소,
    (
        
        -- 전화번호 사이에 - 넣기
        CONCAT_WS('-', LEFT(TLNO,3), SUBSTRING(TLNO, 4, 4) , SUBSTRING(TLNO, 8, 4))
    ) AS 전화번호

FROM
    USED_GOODS_USER
    
WHERE
    USER_ID IN (
        SELECT
            WRITER_ID
        FROM
            USED_GOODS_BOARD
        GROUP BY
            WRITER_ID
        HAVING
            COUNT(BOARD_ID) >= 3 -- 게시판에 글을 3개 이상 올린 USER_ID만 조회
    )

ORDER BY
    USER_ID DESC
```
### 🔗 풀이
1. 🤔 중고 거래 게시물을 3건 이상 등록한 사용자
    - `USED_GOODS_BOARD`에 3번 이상 존재
    
2. 🤔 사용자 ID, 닉네임, 전체주소, 전화번호를 조회
    - 전부 `USED_GOODS_USER`에 있다.
    
3. 🤔 `JOIN`이 필요없는 이유?
    - 우리에게 필요한 것은 `USER_ID`이다. 조건은? `USED_GOODS_BOARD`에 3번 이상 등장한 `USER_ID`
### 🔗 배운점
1. `CONCAT_WS(구분자, st1, st2 ...)`
   > 문자열 합칠 때 구분자를 사이에 넣어준다.
   - 특징
     - 합치려는 값 중에 `NULL`이 있으면, 그 `NULL`과 불필요한 구분자를 완벽하게 무시한다.
   - 예시 코드
     ```sql
      -- 예시 1: 전화번호에 하이픈(-) 넣기
      SELECT CONCAT_WS('-', '010', '1234', '5678');
      -- ➡️ 출력값: '010-1234-5678'
      
      -- 예시 2: 중간에 NULL이 껴있을 때 (주소 데이터)
      SELECT CONCAT_WS(' ', '서울특별시', '강남구', NULL);
      -- ➡️ 출력값: '서울특별시 강남구' (NULL과 쓸데없는 공백이 깔끔하게 무시됨!)
     ```
2. `SUBSTRING(원본_문자열, 시작_위치, 가져올_길이)`
   > 긴 문자열에서 딱 필요한 부분만 잘라낼 때 사용하는 함수이다.
   - 예시 코드
     ```sql
      -- 예시 1: 4번째 자리부터 4글자 가져오기 (전화번호 중간 자리)
      SELECT SUBSTRING('01012345678', 4, 4);
      -- ➡️ 출력값: '1234'
      
      -- 예시 2: 8번째 자리부터 4글자 가져오기 (전화번호 끝 자리)
      SELECT SUBSTRING('01012345678', 8, 4);
      -- ➡️ 출력값: '5678'
      
      -- 예시 3: 길이를 생략하고 8번째 자리부터 끝까지 가져오기
      SELECT SUBSTRING('01012345678', 8);
      -- ➡️ 출력값: '5678'
     ```
3. SQL에서 `NULL`이 작동하는 방식
   1. 연산과 결합에서의 `NULL`
      - 연산하거나 문자열을 합칠 때, `NULL`이 하나라도 있다면, 그 행 자체가 사라진다.
        ```sql
          -- '서울시 테헤란로 NULL'이 되는 게 아님!
          -- 값 전체가 폭파되어 쌩으로 NULL이 나옴.
          SELECT CONCAT('서울시', ' ', '테헤란로', ' ', NULL); 
          -- ➡️ 출력값: NULL
        ```
        ```sql
          SELECT 100 + 50 + NULL;
          -- ➡️ 출력값: NULL (150이 아님!)
        ```
   2. 조건절(`WHERE`)에서의 `NULL`
      - `NULL`은 '알 수 없는 값'이기 때문에, 일반적인 비교 연산자(`=`, `!=`, `<`, `>`)로는 찾을 수가 없다.
      - 틀린 방법
        ```sql
          WHERE STREET_ADDRESS2 = NULL  -- (X) 작동 안 함
          WHERE STREET_ADDRESS2 != NULL -- (X) 작동 안 함
        ```
      - 맞는 방법
        ```sql
          WHERE STREET_ADDRESS2 IS NULL      -- (O) 주소가 없는 사람 찾기
          WHERE STREET_ADDRESS2 IS NOT NULL  -- (O) 주소가 있는 사람 찾기
        ```
   3. 집계 함수에서의 `NULL`
       - `SUM`, `AVG`, `COUNT` 같은 그룹 함수(집계 함수)를 쓸 때, `NULL`은 철저하게 없는 셈치고 계산한다.
       - 만약 테이블이 다음과 같을 때,
         | **STUDENT_NAME** | **SCORE** |
          | --- | --- |
          | A | 100 |
          | B | 80 |
          | C | **NULL** (결시) |
          | D | 60 |
         ```sql
            SELECT SUM(SCORE) FROM EXAM_RESULT; // -> 100 + 80 + NULL + 60 = 240 (NULL은 없는 셈 친다)
         ```
         ```sql
            SELECT AVG(SCORE) FROM EXAM_RESULT; // -> 240 / 3 = 80 (NULL은 사람으로 안 친다.)
         ```
      - 주의사항 `COUNT()`
          ```sql
            -- ① 특정 컬럼(SCORE) 지정
            SELECT COUNT(SCORE) FROM EXAM_RESULT;
            -- ➡️ 출력값: 3 (NULL인 C를 빼고, 점수가 있는 데이터만 센다)
            
            -- ② 별표(*) 사용
            SELECT COUNT(*) FROM EXAM_RESULT;
            -- ➡️ 출력값: 4 (NULL이 있든 말든 '학생(행)이 총 몇 명인가?'를 센다)
        ```
  4. `NULL` 대체함수
     1. `IFNULL()`
        ```sql
          -- 주소가 NULL이면 '주소없음'으로 바꿔서 출력해라
          SELECT IFNULL(STREET_ADDRESS2, '주소없음');
        ```
     2. `COALESCE()`
        ```sql
        -- 앞에서부터 쓱 훑으면서 처음으로 NULL이 아닌 '핸드폰' 번호를 딱 뱉어냄!
          SELECT COALESCE(HOME_PHONE, WORK_PHONE, MOBILE_PHONE, '연락처 없음') AS MAIN_CONTACT;
        ```
        - 작동 흐름: `HOME_PHONE` 확인(`NULL`) ➡️ `WORK_PHONE` 확인(`NULL`) ➡️ `MOBILE_PHONE` 확인(값 있음!) ➡️ '010-1111-2222' 출력.
        - 만약 3개 다 `NULL`이었다면 마지막 방어막인 '연락처 없음'이 출력된다.
