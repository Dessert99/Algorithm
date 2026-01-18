[https://school.programmers.co.kr/learn/courses/30/lessons/132201?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/132201?language=mysql)
```sql
SELECT PT_NAME,PT_NO, GEND_CD, AGE, IFNULL(TLNO, 'NONE') AS TLNO

FROM PATIENT

WHERE AGE <= 12 AND GEND_CD = 'W'

ORDER BY AGE DESC, PT_NAME
```

### 🔗 문제 풀이
1. 타겟 `SELECT`
    - 환자이름
    - 환자번호
    - 성별코드
    - 나이
    - 전화번호
2. 조건 `WHERE`
    - 12세 이하
    - 여성
3. 예외 `IFNULL`
    - 전화번호가 없는 경우, 'NONE'으로 출력
4. 정렬 `ORDER BY`
    - 나이를 기준으로 내림차순 정렬
    - 나이 같다면 환자이름을 기준으로 오름차순 정렬
  
### 🔗 배운점
1. “만약 뭐가 없을 때 대체”를 코드로 표현하기 위한 방법
      - `IFNULL`
          
          ```sql
          -- 문법: IFNULL(컬럼명, '대체할 값')
          SELECT IFNULL(TLNO, 'NONE') AS TLNO
          FROM PATIENT
          ```
          
      - `CASE WHEN`
          
          ```sql
          -- 문법: CASE WHEN 조건 THEN 결과 ELSE 그외값 END
          SELECT 
              CASE 
                  WHEN TLNO IS NULL THEN 'NONE' 
                  WHEN TLNO LIKE '010%' THEN 'MOBILE' -- (예시) 추가 조건 가능
                  ELSE TLNO 
              END AS TLNO
          FROM PATIENT
          ```
