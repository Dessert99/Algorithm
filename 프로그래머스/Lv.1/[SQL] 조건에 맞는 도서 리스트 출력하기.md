[https://school.programmers.co.kr/learn/courses/30/lessons/144853?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/144853?language=mysql)
```sql

SELECT BOOK_ID, DATE_FORMAT(PUBLISHED_DATE, '%Y-%m-%d') AS PUBLISHED_DATE

FROM BOOK

WHERE CATEGORY = '인문' AND YEAR(PUBLISHED_DATE) = 2021

ORDER BY PUBLISHED_DATE
```
### 🔗 풀이
1. 타겟 (SELECT)
    - 도서 ID(`BOOK_ID`), 출판일 (`PUBLISHED_DATE`)을 출력
2. 조건 (WHERE)
    - `2021년`에 출판된
    - `'인문'` 카테고리에 속하는
3. 정럴 (ORDER BY)
    - 출판일을 기준으로 오름차순 정렬

### 🔗 배운점
1. `DATE` 타입의 년(Year) 값을 어떻게 확인할까?
   - YEAR() 함수
        ```sql
        WHERE YEAR(PUBLISHED_DATE) = 2021
        ```
   - LIKE 연산자 (문자열 처럼 검색)
        ```sql
        WHERE PUBLISHED_DATE LIKE '2021%'
        ```
3. `DATE` 타입 포맷 맞추기
    >🚨 문제: `DATE` 타입 컬럼을 그냥 `SELECT` 하면 시분초(`00:00:00`)까지 같이 나온다.
    
    ```sql
    SELECT BOOK_ID, DATE_FORMAT(PUBLISHED_DATE, '%Y-%m-%d') AS PUBLISHED_DATE
    ```
    이렇게 써야 년-월-일만 포맷팅된다.
    꼭 `AS PUBLISHED_DATE`를 붙여야 컬럼명을 설정할 수 있다.
