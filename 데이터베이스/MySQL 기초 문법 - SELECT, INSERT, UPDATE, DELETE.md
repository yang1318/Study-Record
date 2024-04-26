## SELECT
- 데이터를 검색할 때 쓰는 DQL(질의어)  
- 검색 결과는 테이블의 형태로 반환됨  
### 기본 검색 : SELECT ~ FROM ~
- SELECT 키워드와 함께 검색하고 싶은 속성의 이름 나열 + FROM 키워드와 함께 검색하고 싶은 속성이 있는 테이블의 이름 나열  
    ```sql
    SELECT 속성_리스트
    FROM 테이블_리스트;
    ```
- as 키워드를 활용해 속성의 이름을 바꾸어 출력 가능  
    ```sql
    SELECT 키위 as 과일
    FROM 음식;
    ```
- DISTINCT 키워드와 함께 사용하면 중복된 결과를 여러번 보여주지 않음  
    ```sql
    SELECT DISTINCT 속성
    FROM 테이블;
    ```
### 산술 검색 : +, -, *, /
- 산술식(+, -, *, /)을 이용해서도 검색할 수 있음  
    ```sql
    SELECT 현재가격 - 기존가격 AS 인상가
    FROM 테이블;
    ```
### 조건 검색 : WHERE
- 조건을 만족하는 데이터만 검색할 수 있음  
- WHERE 키워드와 함께 비교 연산자, 논리연산자를 이용해 조건 검색  
    ```sql
    SELECT *
    FROM 직원
    WHERE age >= 15;
    ```

- 비교 연산자로 숫자 뿐만 아니라 문자나 날짜 값을 비교하는 것도 가능  
    - ex) `'A' < 'C'`                   => True
    - ex) `'2024-04-01' < '2024-04-24'` => True

- 논리연산자: AND, OR => 여러 조건을 동시에 만족하는지 검색할 수 있음  

#### WHERE과 LIKE를 이용한 검색 : LIKE
- 문자열에 특정 문자가 포함되어 있는지 검색하고 싶으면 LIKE 함수를 사용할 수 있음.  
- LIKE 함수는 WHERE 절에서 사용하고, 와일드카드 문자 %, _ 와 함께 쓰임  
    ```sql
    SELECT name
    FROM 직원
    WHERE city LIKE '%대구%'; -- city에 대구가 들어가는 직원의 이름 검색
    ```

- %(퍼센트) : 0개 이상의 문자를 뜻함  
    - ex) LIKE '%hi' : 앞에 어떤 문자가 오든, 안오든 **맨 마지막이 "hi"로 끝나는 문자열**  
    - ex) LIKE 'hi%' : 뒤에 문자가 있든 없든, **"hi"로 시작하는 문자열**  
    - ex) LIKE '%hi%': **중간에 "hi"가 포함된 문자열**  
- _(언더바) : 1개의 문자를 뜻함  
    - ex) LIKE 'h_i_' : 4글자이고, 첫글자와 세번째 글자가 각각 h와 i 인 문자열  
    - ex) LIKE '___'  : 3글자인 문자열  

- 추가 사용 예시
    - ex) LIKE '_한%' : 두번째 글자가 "한" 인 문자열  

#### WHERE과 NULL을 이용한 검색 : IS NUL, IFNULL
- 특정 속성의 값이 널 값인지를 비교할 때 `**IS NULL**` 사용
    ```sql
    SELECT * 
    FROM 테이블
    WHERE 지도교수 IS NULL;
    ```
- NULL 값이 아닌지 볼 때는 `**IS NOT NULL**`
    ```sql
    SELECT * 
    FROM 테이블
    WHERE 지도교수 IS NOT NULL;

- 필드 값이 NULL 일 때 다른 값으로 대체하여 출력해야할 때 `IFNULL` 함수 사용 가능
    ```sql
    SELECT IFNULL(지도교수, '지도교수 없음')
    FROM 테이블;
    ```

### 검색 결과를 정렬하여 나타내기 : ORDER BY
- 오름차순 (기본값)
    ```sql
    SELECT *
    FROM 테이블
    ORDER BY 컬럼1 ASC;
    ```
- 내림차순
    ```sql
    SELECT *
    FROM 테이블
    ORDER BY 컬럼1 DESC;
    ```
- 여러개를 정렬할 때 : 우선순위가 높은 순서대로 나열
    ```sql
    SELECT *
    FROM 테이블
    ORDER BY status DESC, created_at DESC;
    -- status를 기준으로 내림차순 정렬후, status가 같은 값에 한해서 craeted_at로 내림차순 정렬
    ```
- 특정 값을 우선적으로 정렬하고 싶을 때, CASE 문에 조건을 넣고 THEN 뒤에 정렬 순위를 넣으면 됨.
    ```sql
    SELECT id, title, start_at, end_at
    FROM movie
    ORDER BY CASE
        WHEN status = 1 THEN 1 -- 1순위 
        WHEN status = 2 THEN 2 -- 2순위
        ELSE 3 -- 3순위
    END ASC
    ```

### 그룹별 검색 : GROUP BY
- GROUP BY 절은 선택된 레코드의 집합을 필드의 값이나 표현식에 의해 그룹화한 결과 집합을 반환함
    - 마치 DISTINCT 처럼
- GROUP BY 절은 SELECT 문에서만 사용할 수 있으며, 앞서 살펴 본 그룹 함수를 사용할 때 자주 같이 사용됨

- GROUP BY 절에 사용된 필드의 이름은 반드시 SELECT 문에도 같이 명시되어야 합니다.
    ```sql
    SELECT 필드이름1, 집계함수(필드이름2)
    FROM 테이블이름
    [WHERE 조건]
    GROUP BY 필드이름1
    [HAVING 조건];
    ````
- HAVING 절은 GROUP BY 절에 의해 반환되는 결과 집합의 조건을 설정할 수 있게 해줌
    ```sql
    SELECT Address, Name, MAX(Age) AS MaxAge
    FROM Customer
    GROUP BY Address
    HAVING MaxAge > 15;
    ```
    - WHERE절은 GROUP BY 연산보다 먼저 실행되고, 그 결과에 대해 GROUP BY가 적용되게 됨.
    - HVAGING 절은 그룹으로 묶여진 결과 집합에 조건이 적용되게 됨 => 집계함수도 사용가능함.
