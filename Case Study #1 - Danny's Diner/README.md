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

## Question and Solution

**1. What is the total amount each customer spent at the restaurant?**

````sql
SELECT sales.customer_id, SUM(menu.price) AS amount_spent
FROM sales
LEFT JOIN menu 
    ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
ORDER BY sales.customer_id ASC;
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
SELECT sales.customer_id, COUNT(DISTINCT sales.order_date) AS num_visits
FROM sales
GROUP BY sales.customer_id
ORDER BY num_visits DESC;
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

````sql
WITH ranked_orders AS (
    SELECT 
        sales.customer_id, 
        menu.product_name,
        COUNT(sales.product_id) AS product_count,
        DENSE_RANK() OVER (
            PARTITION BY sales.customer_id 
            ORDER BY COUNT(sales.product_id) DESC
        ) AS rank
    FROM sales
    INNER JOIN menu 
        ON sales.product_id = menu.product_id
    GROUP BY sales.customer_id, menu.product_name
)

SELECT 
    customer_id, 
    product_name,
    product_count
FROM ranked_orders
WHERE rank = 1;
````

#### Answer:
| customer_id | product_name | order_count |
| ----------- | ---------- |------------  |
| A           | ramen        |  3   |
| B           | sushi        |  2   |
| B           | curry        |  2   |
| B           | ramen        |  2   |
| C           | ramen        |  3   |

- Customer A and C's favourite item is ramen.
- Customer B enjoys all items on the menu.

***

**6. Which item was purchased first by the customer after they became a member?**

````sql
WITH ranked_order_date AS (
    SELECT 
        sales.customer_id, 
        sales.product_id,
        RANK() OVER (
            PARTITION BY sales.customer_id 
            ORDER BY sales.order_date ASC
        ) AS rank
    FROM sales
    INNER JOIN members 
        ON sales.customer_id = members.customer_id
        AND sales.order_date > members.join_date
)

SELECT 
    ranked_order_date.customer_id, 
    menu.product_name
FROM ranked_order_date
INNER JOIN menu 
    ON ranked_order_date.product_id = menu.product_id
WHERE rank = 1
ORDER BY ranked_order_date.customer_id ASC;
````

#### Answer:
| customer_id | product_name |
| ----------- | ---------- |
| A           | ramen        |
| B           | sushi        |

- Customer A's first order as a member is ramen.
- Customer B's first order as a member is sushi.

***

**7. Which item was purchased just before the customer became a member?**

````sql
WITH purchased_prior_member AS (
    SELECT 
        sales.customer_id, 
        sales.product_id,
        sales.order_date,
        members.join_date,
        RANK() OVER (
            PARTITION BY sales.customer_id 
            ORDER BY sales.order_date DESC
        ) AS rank
    FROM sales
    INNER JOIN members 
        ON sales.customer_id = members.customer_id
        AND sales.order_date < members.join_date
)

SELECT 
    p_member.customer_id, 
    menu.product_name
FROM purchased_prior_member AS p_member 
INNER JOIN menu 
    ON p_member.product_id = menu.product_id
WHERE p_member.rank = 1
ORDER BY p_member.customer_id ASC;
````

#### Answer:
| customer_id | product_name |
| ----------- | ------------ |
| A           | sushi        |
| A           | curry        |
| B           | sushi        |

- Customer A's last order was both sushi and curry.
- Customer B's last order was sushi.

**8. What is the total items and amount spent for each member before they became a member?**

````sql
SELECT 
    sales.customer_id,
    COUNT(sales.product_id) AS total_items,
    SUM(menu.price) AS amount_spent
FROM sales
INNER JOIN members 
    ON sales.customer_id = members.customer_id
    AND sales.order_date < members.join_date
INNER JOIN menu 
    ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
ORDER BY sales.customer_id ASC;
````

#### Answer:
| customer_id | total_items | total_sales |
| ----------- | ---------- |----------  |
| A           | 2 |  25       |
| B           | 3 |  40       |

Before becoming members,
- Customer A spent $25 on 2 items.
- Customer B spent $40 on 3 items.

***

**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier — how many points would each customer have?**

````sql
SELECT 
    sales.customer_id, 
    SUM(
        CASE 
            WHEN sales.product_id = '1' THEN 20 * menu.price
            ELSE 10 * menu.price
        END
    ) AS total_points
FROM sales
LEFT JOIN menu 
    ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
ORDER BY total_points DESC;
````

#### Answer:
| customer_id | total_points | 
| ----------- | ------------ |
| B           | 940          |
| A           | 860          |
| C           | 360          |

- Total points for Customer A is $860.
- Total points for Customer B is $940.
- Total points for Customer C is $360.

***

**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi — how many points do customer A and B have at the end of January?**

```sql
WITH dates_cte AS (
  SELECT 
    customer_id, 
      join_date, 
      join_date + 6 AS valid_date, 
      DATE_TRUNC(
        'month', '2021-01-31'::DATE)
        + interval '1 month' 
        - interval '1 day' AS last_date
  FROM members
)

SELECT 
  sales.customer_id, 
  SUM(CASE
    WHEN menu.product_name = 'sushi' THEN 2 * 10 * menu.price
    WHEN sales.order_date BETWEEN dates.join_date AND dates.valid_date THEN 2 * 10 * menu.price
    ELSE 10 * menu.price END) AS points
FROM sales
INNER JOIN dates_cte AS dates
  ON sales.customer_id = dates.customer_id
  AND dates.join_date <= sales.order_date
  AND sales.order_date <= dates.last_date
INNER JOIN menu
  ON sales.product_id = menu.product_id
GROUP BY sales.customer_id;
````

#### Answer:
| customer_id | total_points | 
| ----------- | ------------ |
| A           | 1020         |
| B           | 320          |

- Total points for Customer A is 1,020.
- Total points for Customer B is 320.

***
