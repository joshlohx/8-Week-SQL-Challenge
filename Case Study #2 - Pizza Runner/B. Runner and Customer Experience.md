# 🍕 Case Study #2 Pizza Runner

## Solution - B. Runner and Customer Experience

### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

````sql
SELECT 
    ((registration_date - '2021-01-01') / 7) + 1 AS weeknr,
    registration_date - ((registration_date - '2021-01-01') % 7) AS week_start_date,
    COUNT(*) AS sign_ups
FROM runners
GROUP BY weeknr, week_start_date
ORDER BY week_start_date;
````

**Answer:**

![image](https://github.com/user-attachments/assets/3390832e-865f-461b-95dc-af8c59ad25e6)


- On Week 1 of Jan 2021, 2 new runners signed up.
- On Week 2 and 3 of Jan 2021, 1 new runner signed up.

### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

````sql
SELECT 
    runner_id, 
    DATE_TRUNC('minute', AVG(pickup_time - order_time)) AS average_pickup_time
FROM customer_orders_temp AS cust
INNER JOIN runner_orders_temp AS runner
    ON cust.order_id = runner.order_id
WHERE pickup_time IS NOT NULL
GROUP BY runner_id
ORDER BY runner_id ASC;
````

**Answer:**

![image](https://github.com/user-attachments/assets/2b9f8902-d9f9-4583-b315-ac66d70f2690)

- The average time taken in minutes by runner 1 is 15 minutes.
- The average time taken in minutes by runner 2 is 23 minutes.
- The average time taken in minutes by runner 3 is 10 minutes.

*Note: This solution assumed we want the minute taken by each runner ROUNDED DOWN.*

### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?

````sql
WITH temp AS (
    SELECT 
        cust.order_id,
        DATE_TRUNC('minute', pickup_time - order_time) AS prepare_time,
        COUNT(*) AS pizza_num
    FROM customer_orders_temp AS cust
    INNER JOIN runner_orders_temp AS runner
        ON cust.order_id = runner.order_id
    WHERE pickup_time IS NOT NULL
    GROUP BY cust.order_id, prepare_time
)

SELECT 
    pizza_num, 
    AVG(prepare_time) AS average_prepare_time
FROM temp
GROUP BY pizza_num
ORDER BY pizza_num ASC;
````

**Answer:**

![image](https://github.com/user-attachments/assets/9f0f17fc-2b43-402e-a1f0-26fc11ff47e8)


- On average, a single pizza order takes 12 minutes to prepare and an order with 3 pizzas takes 29 minutes.
- It takes 18 minutes to prepare an order with 2 pizzas which is 9 minutes per pizza — making 2 pizzas in a single order the ultimate efficiency rate.

### 4. Is there any relationship between the number of pizzas and how long the order takes to prepare?

````sql

````

**Answer:**

![image]()

_(Assuming that distance is calculated from Pizza Runner HQ to customer’s place)_

- Customer 104 stays the nearest to Pizza Runner HQ at average distance of 10km, whereas Customer 105 stays the furthest at 25km.

### 5. What was the difference between the longest and shortest delivery times for all orders?

Firstly, I'm going to filter results with non-null duration first just to have a feel. You can skip this step and go straight to the answer.

````sql

````

<img width="269" alt="image" src="">

```sql

```

**Answer:**

<img width="196" alt="image" src="">

- The difference between longest (40 minutes) and shortest (10 minutes) delivery time for all orders is 30 minutes.

### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

````sql

````

**Answer:**

![image]()

_(Average speed = Distance in km / Duration in hour)_
- Runner 1’s average speed runs from 37.5km/h to 60km/h.
- Runner 2’s average speed runs from 35.1km/h to 93.6km/h. Danny should investigate Runner 2 as the average speed has a 300% fluctuation rate!
- Runner 3’s average speed is 40km/h

### 7. What is the successful delivery percentage for each runner?

````sql

````

**Answer:**

![image]()

- Runner 1 has 100% successful delivery.
- Runner 2 has 75% successful delivery.
- Runner 3 has 50% successful delivery

***Click [here]() for solution for C. Ingredient Optimisation!***
