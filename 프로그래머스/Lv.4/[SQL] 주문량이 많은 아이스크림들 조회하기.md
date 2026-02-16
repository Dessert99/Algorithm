[https://school.programmers.co.kr/learn/courses/30/lessons/133027?language=mysql](https://school.programmers.co.kr/learn/courses/30/lessons/133027?language=mysql)
```sql
SELECT FLAVOR

FROM (
    SELECT FLAVOR, TOTAL_ORDER FROM FIRST_HALF
    UNION ALL
    SELECT FLAVOR, TOTAL_ORDER FROM JULY
) AS NEW_TABLE -- UNION 시 꼭 테이블 별칭을 달아야 한다.

GROUP BY FLAVOR

ORDER BY SUM(TOTAL_ORDER) DESC

LIMIT 3
```
### 🔗 풀이
1. 👀 방법1)
    1. 두 테이블을 `UNION` 으로 합친다. 컬럼 개수, 종류가 같기 때문이다. -> ❌ -> `UNION ALL`로 합쳐야 한다.`
    2. `FLAVOR` 기준으로 `GROUP`한다. 
    3. `TOTAL_ORDER` 기준으로 정렬하여 상위 3개를 조회한다.
### 🔗 배운점
1. 같은 아이스크림에 대하여 서로 다른 두 공장에서 아이스크림 가게로 출하를 진행하는 경우가 있습니다. 이 경우 같은 맛의 아이스크림이라도 다른 출하 번호를 갖는다.
    - `SHIPMENT_ID`와 `FLAVOR`이 1대1 매칭이 아니라는 의미이다.

2. `FLAVOR`는 `FIRST_HALF` 테이블의 `FLAVOR`의 외래 키, `FIRST_HALF`테이블의 `SHIPMENT_ID`는 `JULY`테이블의 `SHIPMENT_ID`의 외래 키라는 말의 의미
    - `JULY` 테이블에 있는 `SHIPMENT_ID`, `FLAVOR`는 전부 `FIRST_HALF`에 있다는 말이다.

3. `FIRST_HALF` 테이블의 기본 키는 `FLAVOR`라는 의미
    - 7월에는 같은 맛에 출하번호가 여러 개일 수도 있지만 상반기 테이블에서 `FLAVOR`는 출하번호와 1대1 매칭된다는 의미이다.

4. `UNION ALL`을 써야 하는 이유
    - `UNION`은 동일한 데이터를 지운다. 예를 들어, 상반기, 7월 모두 100개가 팔린 딸기맛이 있다면 하나를 지운다. 하지만 우리는 200개로 집계해야하기 때문에 모두 필요하다.
    - 이를 위해서 모든 행을 살려주는 `UNION ALL`을 써야 한다.

5. 테이블에 별칭을 달아야 하는 이유
    - `FROM` 절에서 만든 새로운 테이블(파생 테이블)은 꼭 이름표를 달아야 한다.
    - 왜? 메모리에 새로운 테이블을 만들었는데, 불러오려면 이름을 사용해야 해서.
    - 유사한 상황으로, 셀프 조인할 때도 같은 테이블을 구분하려면 각각 별칭을 달아줘야 한다.
