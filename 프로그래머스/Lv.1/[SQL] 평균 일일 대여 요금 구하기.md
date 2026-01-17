[https://school.programmers.co.kr/learn/courses/30/lessons/151136?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/151136?language=mysql)
```SQL
SELECT
    ROUND(AVG(DAILY_FEE), 0) AS AVERAGE_FEE
FROM
    CAR_RENTAL_COMPANY_CAR
WHERE
    CAR_TYPE = 'SUV'
```
### 🔗 배운점
1. SQL 반올림 방법
   ```SQL
      ROUND(값, 남길 자리)
    # ROUND(1.34) -> 1
    # ROUND(1.55, 1) -> 1.5
    # ROUND(1.543, 2) -> 1.54
   ```
2. 컴퓨터가 읽는 SQL 순서
   | 실행 순서 | SQL 키워드 | 설명 (컴퓨터의 생각) | 자바스크립트 비유 |
    | :---: | :--- | :--- | :--- |
    | **1** | **FROM** (+ JOIN) | "일단 냉장고에서 재료(테이블) 다 꺼내!" | `const data = [...table];` |
    | **2** | **ON** | "(JOIN 할 때) 이 조건에 맞는 애들끼리 붙여!" | `(Merge Logic)` |
    | **3** | **WHERE** | "상한 재료(조건 안 맞는 행)는 다 갖다 버려!" | `.filter(item => condition)` |
    | **4** | **GROUP BY** | "같은 종류끼리 바구니에 담아(그룹화)!" | `.reduce((acc, cur) => ...)` |
    | **5** | **HAVING** | "바구니 중에서 무게 미달인 바구니는 버려!" | `(Group Filter)` |
    | **6** | **SELECT** | "이제 손님한테 보여줄 모양(컬럼)으로 썰어!" | `.map(item => result)` |
    | **7** | **DISTINCT** | "똑같은 모양은 하나만 남겨!" | `new Set(data)` |
    | **8** | **ORDER BY** | "보기 좋게 줄 세워(정렬)!" | `.sort((a, b) => ...)` |
    | **9** | **LIMIT** | "앞에서 5개만 접시에 담아!" | `.slice(0, 5)` |
