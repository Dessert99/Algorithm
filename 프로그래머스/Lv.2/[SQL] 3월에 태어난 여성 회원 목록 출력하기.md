[https://school.programmers.co.kr/learn/courses/30/lessons/131120?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/131120?language=mysql)
```sql
SELECT MEMBER_ID, MEMBER_NAME, GENDER, DATE_FORMAT(DATE_OF_BIRTH, '%Y-%m-%d') AS DATE_OF_BIRTH

FROM MEMBER_PROFILE

WHERE  DATE_OF_BIRTH LIKE "%-03-%" AND GENDER = 'W' AND TLNO IS NOT NULL

ORDER BY MEMBER_ID
```
### 🔗 풀이
1. 조회 (SELECT)
    - ID
    - 이름
    - 성별
    - 생년월일
2. 조건 (WHERE)
    - 생일이 3월
        ```sql
          ❌ MONTH(DATE_OF_BIRTH) = '03' 
          ✅ MONTH(DATE_OF_BIRTH) = 3
        ```
        - 더 효율적으로 조회하려면 다음 방법이 있다.
        ```sql
          ✅ DATE_OF_BIRTH LIKE "%-03-%"
        ```
        
    - 여성 회원
    - 전화번호가 NULL인 경우는 출력대상에서 제외
        ```sql
          ❌ TLNO != 'NULL'
          ✅ TLNO IS NOT NULL
        ```
        
3. 정렬 (ORDER BY)
    - 회원ID를 기준으로 오름차순 정렬

### 🔗 배운점
1. MONTH()는 숫자를 반환한다.
    
    ```sql
      MONTH(DATE_OF_BIRTH) = 3
    ```
    
2. ‘NULL값이 아닌 경우’  = `IS NOT NULL`
    
    ```sql
      TLNO IS NOT NULL
    ```
