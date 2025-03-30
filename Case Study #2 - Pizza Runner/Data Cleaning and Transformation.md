# 🍕 Case Study #2 - Pizza Runner

## 🧼 Data Cleaning & Transformation

### 🔨 Table: customer_orders

Looking at the `customer_orders` table below, we can see that there are
- In the `exclusions` column, there are missing/ blank spaces ' ' and null values. 
- In the `extras` column, there are missing/ blank spaces ' ' and null values.

<img width="1063" alt="image" src="https://user-images.githubusercontent.com/81607668/129472388-86e60221-7107-4751-983f-4ab9d9ce75f0.png">

Our course of action to clean the table:
- Create a temporary table with all the columns
- Remove blank space '' values in `exlusions` and `extras` columns and replace with null.

````sql
CREATE TEMP TABLE customer_orders_temp AS
SELECT 
    order_id, 
    customer_id, 
    pizza_id, 
    NULLIF(TRIM(exclusions), '') AS exclusions,
    NULLIF(TRIM(extras), '') AS extras,
    order_time
FROM pizza_runner.customer_orders;
`````

This is how the clean `customers_orders_temp` table looks like and we will use this table to run all our queries.

<img width="1058" alt="image" src="https://private-user-images.githubusercontent.com/170286077/412668088-cdecc928-a760-4003-ab39-7851a193a5f0.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3Mzk0MDc4NDEsIm5iZiI6MTczOTQwNzU0MSwicGF0aCI6Ii8xNzAyODYwNzcvNDEyNjY4MDg4LWNkZWNjOTI4LWE3NjAtNDAwMy1hYjM5LTc4NTFhMTkzYTVmMC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUwMjEzJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MDIxM1QwMDQ1NDFaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT04MjFmZDQ2ZWQ3ODdiN2I1ZTY5ZmNlOTU4ODIzMWE1ZjU0MTExNjEyNzNlYzg1NjgwMWYyZTI0Y2YxM2Q4NGI5JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.F9Z4z1NUwVhjem1NzQOFSv3BdcDyZV8q56Z-SbLNcN8">

***

### 🔨 Table: runner_orders

Looking at the `runner_orders` table below, we can see that there are
- In the `exclusions` column, there are missing/ blank spaces ' ' and null values. 
- In the `extras` column, there are missing/ blank spaces ' ' and null values

<img width="1037" alt="image" src="https://user-images.githubusercontent.com/81607668/129472585-badae450-52d2-442e-9d50-e4d0d8fce83a.png">

Our course of action to clean the table:
- In `pickup_time` column, remove 'null' (null as a string, not a true null value) and remove blank space '' and replace with null.
- In `distance` column, remove "km" and blank space '' and replace with null.
- In `duration` column, remove "minutes", "minute" and blank space '' and replace with null.
- In `cancellation` column, remove 'null' (null as a string, not a true null value) and blank space ' ' and replace with null.

````sql
CREATE TEMP TABLE runner_orders_temp AS
SELECT
    order_id,
    runner_id,
    CASE
        WHEN TRIM(pickup_time) = 'null' THEN NULL
        ELSE NULLIF(TRIM(pickup_time), '')
    END AS pickup_time,  -- Replace 'null' with NULL
    NULLIF(REGEXP_REPLACE(TRIM(distance), '[^0-9]', '', 'g'), '') AS distance,
    NULLIF(REGEXP_REPLACE(TRIM(duration), '[^0-9]', '', 'g'), '') AS duration,
    CASE
    	WHEN TRIM(cancellation) = 'null' THEN NULL
        ELSE NULLIF(TRIM(cancellation), '') 
    END AS cancellation
FROM pizza_runner.runner_orders;
````

Then, we alter the `pickup_time`, `distance` and `duration` columns to the correct data type.

````sql
ALTER TABLE runner_orders_temp
ALTER COLUMN pickup_time TYPE TIMESTAMP WITHOUT TIME ZONE using pickup_time::TIMESTAMP WITHOUT TIME ZONE,
ALTER COLUMN distance TYPE FLOAT USING distance::FLOAT,
ALTER COLUMN duration TYPE INT USING duration::INT;
````

This is how the clean `runner_orders_temp` table looks like and we will use this table to run all our queries.

<img width="915" alt="image" src="https://private-user-images.githubusercontent.com/170286077/412668591-c45cfed2-1e92-4b96-b303-29f889f0a1b9.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3Mzk0MDc5OTIsIm5iZiI6MTczOTQwNzY5MiwicGF0aCI6Ii8xNzAyODYwNzcvNDEyNjY4NTkxLWM0NWNmZWQyLTFlOTItNGI5Ni1iMzAzLTI5Zjg4OWYwYTFiOS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUwMjEzJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MDIxM1QwMDQ4MTJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1iYzBkY2RmNDdhM2JjM2RhMDUwZTc3MmNmODMzYTcyNzJlNzNhZGVlOTBlNGY1ODZhZjk0ODk0OWYwZjQ1ZGQ5JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.JOtHPlIhLclDpFm2rzakkD6xGUT83b8N5AjfFcbVetU">

***

Click here for [solution](https://github.com/joshlohx/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/A.%20Pizza%20Metrics.md) to **A. Pizza Metrics**!
