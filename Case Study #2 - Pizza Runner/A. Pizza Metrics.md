# 🍕 Case Study #2 - Pizza Runner

## 🍝 Solution - A. Pizza Metrics

### 1. How many pizzas were ordered?

````sql
SELECT COUNT(*) as pizzas_ordered
FROM customer_orders_temp
````

**Answer:**

![image](https://github.com/user-attachments/assets/3a90597b-58c6-413c-b923-2077f59dc3de)

- Total of 14 pizzas were ordered.

### 2. How many unique customer orders were made?

````sql
SELECT 
  COUNT(DISTINCT order_id) AS unique_order_count
FROM #customer_orders;
````

**Answer:**

![image](https://github.com/user-attachments/assets/cf45a1ca-c557-4298-a177-75a3d8012443)

- There are 10 unique customer orders.

### 3. How many successful orders were delivered by each runner?

````sql
SELECT runner_id, COUNT(DISTINCT order_id) AS successful_orders
FROM runner_orders_temp
WHERE cancellation IS NULL
GROUP BY runner_id;
````

**Answer:**

![image](https://github.com/user-attachments/assets/9aadbd7a-ee32-4261-987a-778407a2a733)

- Runner 1 has 4 successful delivered orders.
- Runner 2 has 3 successful delivered orders.
- Runner 3 has 1 successful delivered order.

### 4. How many of each type of pizza was delivered?

````sql
SELECT p.pizza_name, COUNT(c.pizza_id) AS delivered_pizza_count
FROM customer_orders_temp c
INNER JOIN runner_orders_temp r 
    ON c.order_id = r.order_id
    AND r.cancellation IS NULL
INNER JOIN pizza_names p
    ON c.pizza_id = p.pizza_id
GROUP BY p.pizza_name
ORDER BY delivered_pizza_count DESC;
````

**Answer:**

![image](https://github.com/user-attachments/assets/f02d97f9-c14b-408d-a8cd-1e9d709593bf)

- There are 9 delivered Meatlovers pizzas and 3 Vegetarian pizzas.

### 5. How many Vegetarian and Meatlovers were ordered by each customer?**

````sql
SELECT c.customer_id, 
       COUNT(*) FILTER (WHERE pizza_id = 1) AS meatlovers_count,
       COUNT(*) FILTER (WHERE pizza_id = 2) AS vegetarian_count
FROM customer_orders_temp c
GROUP BY c.customer_id
ORDER BY c.customer_id ASC;
````

**Answer:**

![image](https://private-user-images.githubusercontent.com/170286077/412680469-73402963-5ec3-43d7-9198-0f115905ea73.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3Mzk0MTE1NzUsIm5iZiI6MTczOTQxMTI3NSwicGF0aCI6Ii8xNzAyODYwNzcvNDEyNjgwNDY5LTczNDAyOTYzLTVlYzMtNDNkNy05MTk4LTBmMTE1OTA1ZWE3My5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUwMjEzJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MDIxM1QwMTQ3NTVaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT00OWVkZTE2YTk4YWUzNWI4MWI3NjI3YjU1NTZkYTYyYjBlMjk4NzZjNTQ2MTUzNjhlZWUzMjZmNjBhZjMxMzg0JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.1Go53i_c7C_1_3bdDd2FbMdOSYejsplkCrNTdmfYbGE)

- Customer 101 ordered 2 Meatlovers pizzas and 1 Vegetarian pizza.
- Customer 102 ordered 2 Meatlovers pizzas and 1 Vegetarian pizzas.
- Customer 103 ordered 3 Meatlovers pizzas and 1 Vegetarian pizza.
- Customer 104 ordered 3 Meatlovers pizza.
- Customer 105 ordered 1 Vegetarian pizza.

### 6. What was the maximum number of pizzas delivered in a single order?

````sql
WITH delivered_pizzas_per_order AS (
    SELECT 
        c.order_id, 
        COUNT(c.pizza_id) AS pizza_count
    FROM 
        customer_orders_temp c
    INNER JOIN 
        runner_orders_temp r 
        ON c.order_id = r.order_id
        AND r.cancellation IS NULL
    GROUP BY 
        c.order_id
)

SELECT 
    MAX(pizza_count) AS pizza_count
FROM 
    delivered_pizzas_per_order;
````

**Answer:**

![image](https://github.com/user-attachments/assets/5e45c969-a240-41c3-9b66-6e7ad305a4ab)

- Maximum number of pizza delivered in a single order is 3 pizzas.

### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

````sql
SELECT 
    c.customer_id,
    COUNT(*) FILTER (WHERE exclusions IS NULL AND extras IS NULL) AS unchanged_count,
    COUNT(*) FILTER (WHERE exclusions IS NOT NULL OR extras IS NOT NULL) AS changed_count
FROM 
    customer_orders_temp c
INNER JOIN 
    runner_orders_temp r
    ON c.order_id = r.order_id
    AND r.cancellation IS NULL
GROUP BY 
    c.customer_id
ORDER BY 
    c.customer_id ASC;
````

**Answer:**

![image](https://private-user-images.githubusercontent.com/170286077/412687441-2e2c95ac-620c-42de-84d9-601903086f34.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3Mzk0MTM2MTEsIm5iZiI6MTczOTQxMzMxMSwicGF0aCI6Ii8xNzAyODYwNzcvNDEyNjg3NDQxLTJlMmM5NWFjLTYyMGMtNDJkZS04NGQ5LTYwMTkwMzA4NmYzNC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUwMjEzJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MDIxM1QwMjIxNTFaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1iMmIwNzc0MmY0NzkxZjRkNmIwMWY4YzEyZWMzMmZiY2MzMTk5MmZiZDBmYzQxMTI0YzM3OTUxNDU2YjY2NjM3JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.IKYRd4QNXEjKJ3H-6JYLO76NVeiCQijo7ofdNVWOZUU)

- Customer 101 and 102 likes his/her pizzas per the original recipe.
- Customer 103, 104 and 105 have their own preference for pizza topping and requested at least 1 change (extra or exclusion topping) on their pizza.

### 8. How many pizzas were delivered that had both exclusions and extras?

````sql
SELECT 
    COUNT(*) FILTER (WHERE exclusions IS NOT NULL AND extras IS NOT NULL) AS exclusions_and_extras
FROM 
    customer_orders_temp c
INNER JOIN 
    runner_orders_temp r
    ON c.order_id = r.order_id
    AND r.cancellation IS NULL;
````

**Answer:**

![image](https://github.com/user-attachments/assets/6487e343-762d-4e94-869b-b0cf2ed4591e)

- Only 1 pizza delivered that had both extra and exclusion topping.

### 9. What was the total volume of pizzas ordered for each hour of the day?

````sql
SELECT 
    TO_CHAR(c.order_time, 'HH AM') AS order_hour,
    COUNT(c.pizza_id) AS pizza_count
FROM customer_orders_temp c
GROUP BY order_hour, EXTRACT(HOUR FROM c.order_time)
ORDER BY EXTRACT(HOUR FROM c.order_time);
````

**Answer:**

![image](https://github.com/user-attachments/assets/5b31deb2-b054-40c4-8270-b243905de72a)

- Highest volume of pizza ordered is at 1:00 pm, 9:00 pm and 11:00 pm.
- Lowest volume of pizza ordered is at 11:00 am and 7:00 pm.

### 10. What was the volume of orders for each day of the week?

````sql
SELECT 
    TO_CHAR(c.order_time, 'Day') AS order_day,
    COUNT(c.pizza_id) AS pizza_count
FROM customer_orders_temp c
GROUP BY order_day, TO_CHAR(c.order_time, 'D')
ORDER BY TO_CHAR(c.order_time, 'D') asc;
````

 **Answer:**

![image](https://github.com/user-attachments/assets/832329fd-242a-481f-a120-06a023de348c)

- There are 5 pizzas ordered on Wednesday and Saturday.
- There are 3 pizzas ordered on Thursday.
- There is 1 pizza ordered on Friday.
