## 수학 함수
- FLOOR() : 내림
    ```sql
    SELECT FLOOR(5.5) -- 5
    ```
- CEIL() : 올림
    ```sql
    SELECT CEIL(5.5) -- 6
    ```
- ROUND() : 반올림. 두번째 인수로 반올릴할 소수점을 전달할 수도 있음
    ```sql
    SELECT ROUND(5.5)  -- 6
    ```
     ```sql
    SELECT ROUND(5.556901, 4) -- 5.5569
    ```
