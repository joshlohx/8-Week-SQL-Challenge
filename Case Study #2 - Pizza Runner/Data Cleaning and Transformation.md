# 🍕 Case Study #2 - Pizza Runner

## 🧼 Data Cleaning & Transformation

### 🔨 Table: customer_orders

Looking at the `customer_orders` table below, we can see that there are
- In the `exclusions` column, there are missing/ blank spaces ' ' and null values. 
- In the `extras` column, there are missing/ blank spaces ' ' and null values.

<img width="1063" alt="image" src="![customer_orders](https://github.com/user-attachments/assets/6d8b5908-33b6-4989-87c5-83fa853d9d88)">




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

<img width="1058" alt="image" src="https://user-images.githubusercontent.com/81607668/129472551-fe3d90a0-1e8b-4f32-a2a7-2ecd3ac469ef.png">

***

### 🔨 Table: runner_orders

Looking at the `runner_orders` table below, we can see that there are
- In the `exclusions` column, there are missing/ blank spaces ' ' and null values. 
- In the `extras` column, there are missing/ blank spaces ' ' and null values

<img width="1037" alt="image" src="https://user-images.githubusercontent.com/81607668/129472585-badae450-52d2-442e-9d50-e4d0d8fce83a.png">

Our course of action to clean the table:
- In `pickup_time` column, remove blank space '' and replace with null.
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
    NULLIF(TRIM(cancellation), '') AS cancellation
FROM pizza_runner.runner_orders;
````

Then, we alter the `pickup_time`, `distance` and `duration` columns to the correct data type.

````sql
ALTER TABLE runner_orders_temp
ALTER COLUMN pickup_time TYPE TIMESTAMP WITHOUT TIME ZONE using pickup_time::TIMESTAMP WITHOUT TIME ZONE
ALTER COLUMN distance TYPE FLOAT USING distance::FLOAT,
ALTER COLUMN duration TYPE INT USING duration::INT,
````

This is how the clean `runner_orders_temp` table looks like and we will use this table to run all our queries.

<img width="915" alt="image" src="https://user-images.githubusercontent.com/81607668/129472778-6403381d-6e30-4884-a011-737b1eff7379.png">

***

Click here for [solution](https://github.com/katiehuangx/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/A.%20Pizza%20Metrics.md) to **A. Pizza Metrics**!
