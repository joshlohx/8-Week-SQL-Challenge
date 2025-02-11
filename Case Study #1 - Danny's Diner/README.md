# 🍜 Case Study #1: Danny's Diner 
<img src="https://user-images.githubusercontent.com/81607668/127727503-9d9e7a25-93cb-4f95-8bd0-20b87cb4b459.png" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Question and Solution](#question-and-solution)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-1/). 

***

## Business Task
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. 

## Entity Relationship Diagram

![image](https://user-images.githubusercontent.com/81607668/127271130-dca9aedd-4ca9-4ed8-b6ec-1e1920dca4a8.png)

***

**1. What is the total amount each customer spent at the restaurant?**

````sql
SELECT s.customer_id, SUM(m.price) AS amount_spent 
FROM sales s
LEFT JOIN menu m
ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id ASC;
````

#### Answer:

| customer_id | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

- Customer A spent $76.
- Customer B spent $74.
- Customer C spent $36.

***

**2. How many days has each customer visited the restaurant?**

````sql
select s.customer_id, COUNT(distinct s.order_date) as num_visits
from sales s
group by s.customer_id
order by num_visits DESC;
````

#### Answer:
| customer_id | num_visits |
| ----------- | ---------- |
| B           | 6          |
| A           | 4          |
| C           | 2          |

- Customer B visited 6 times.
- Customer A visited 4 times.
- Customer C visited 2 times.

***

**3. What was the first item from the menu purchased by each customer?**

````sql
WITH earliest_date AS (
    SELECT 
        sales.customer_id, 
        MIN(sales.order_date) AS earliest_date
    FROM sales
    GROUP BY sales.customer_id
)

SELECT DISTINCT 
    s.customer_id, 
    m.product_name
FROM sales s
INNER JOIN earliest_date e 
    ON s.customer_id = e.customer_id 
    AND s.order_date = e.earliest_date
INNER JOIN menu m 
    ON s.product_id = m.product_id
ORDER BY s.customer_id ASC;
````

#### Answer:
| customer_id | product_name | 
| ----------- | ----------- |
| A           | curry        | 
| A           | sushi        | 
| B           | curry        | 
| C           | ramen        |

- Customer A placed an order for both curry and sushi simultaneously, making them the first items in the order.
- Customer B's first order is curry.
- Customer C's first order is ramen.

***

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**

````sql
SELECT 
    menu.product_name, 
    COUNT(sales.product_id) AS purchase_count
FROM sales
INNER JOIN menu 
    ON sales.product_id = menu.product_id
GROUP BY menu.product_name
ORDER BY purchase_count DESC
LIMIT 1;
````

#### Answer:
| most_purchased | product_name | 
| -------------- | ------------ |
| 8              |    ramen     |


- Most purchased item on the menu is ramen which is 8 times.

***

**5. Which item was the most popular for each customer?**
