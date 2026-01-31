[https://school.programmers.co.kr/learn/courses/30/lessons/301647?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/301647?language=mysql)
```sql
SELECT CHILD.ID, CHILD.GENOTYPE, PARENT.GENOTYPE AS PARENT_GENOTYPE

FROM ECOLI_DATA AS CHILD
    JOIN ECOLI_DATA AS PARENT
        ON CHILD.PARENT_ID = PARENT.ID -- 자식과 부모 연결

-- 부모의 형질을 모두 보유한
WHERE (CHILD.GENOTYPE & PARENT.GENOTYPE) = PARENT.GENOTYPE

ORDER BY CHILD.ID
```
### 🔗 풀이
💡 같은 테이블 다른 컬럼끼리 관계를 갖고 있다. (부모-자식)
    -> 셀프 조인

💡 보유한 형질 계산
    - GENOTYPE을 2진수로 나타낸 뒤 값이 1인 자릿수.

🤔 그룹화가 필요할까?
    - 집계 함수가 딱히 필요없어 보인다. 각 개체가 조건을 만족하는지 파악하기 때문이다.

🤔 모든 부모와 형질 비교는 어떻게 할까?
    - 문제 이해를 잘못 했었다. "모든" 부모가 아니라, 직계 부모의 모든 형질을 파악하면 된다.

### 🔗 배운점
1.  `WHERE (CHILD.GENOTYPE & PARENT.GENOTYPE) = PARENT.GENOTYPE` 가 부모의 형질을 모두 보유한 인 이유?
    - 자식이 부모의 형질을 단 하나라도 갖고 있지 않으면 `&`연산 과정에서 그 비트가 0이 되어버려서 부모 형질과 값이 같아질 수 없다.
2. 셀프 조인 시각화
    1. 1단계: 원본 테이블
        
        
        | **ID (내 번호)** | **GENOTYPE (형질)** | **PARENT_ID (부모님 번호)** |
        | --- | --- | --- |
        | **1** | 10 | NULL |
        | **2** | 100 | **1** |
        | **3** | 1000 | **2** |
    2. 테이블 생성
        - `FROM ECOLI_DATA AS CHILD JOIN ECOLI_DATA AS PARENT`를 하는 순간, 메모리 상에는 똑같은 테이블 두 개가 **역할**을 나눠 가집니다.
        
        [왼쪽: 자식 테이블 (CHILD)]
        
        | **C.ID** | **C.GENO** | **C.PARENT_ID (티켓)** |
        | --- | --- | --- |
        | 1 | 10 | NULL |
        | **2** | 100 | **1** |
        | **3** | 1000 | **2** |
        
        [오른쪽: 부모 테이블 (PARENT)]
        
        | **P.ID (좌석 번호)** | **P.GENO** | **P.PARENT_ID** |
        | --- | --- | --- |
        | **1** | 10 | NULL |
        | **2** | 100 | 1 |
        | 3 | 1000 | 2 |
    3. 3단계: 매칭 (ON 조건의 시각화)
        
        > `ON CHILD.PARENT_ID = PARENT.ID`
        > 
    4. 4단계: 최종 결과 (JOIN 완료)
        
        
        | **(자식) ID** | **(자식) GENO** | **(자식) PARENT_ID** | **🔗** | **(부모) ID** | **(부모) GENO** |
        | --- | --- | --- | --- | --- | --- |
        | 2 | 100 | **1** | == | **1** | 10 |
        | 3 | 1000 | **2** | == | **2** | 100 |
