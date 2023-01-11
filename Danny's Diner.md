# 🍽 Case Study #1 - Danny's Diner

![danny's diner]([https://user-images.githubusercontent.com/103854541/211166454-6af0b127-e0d5-4c20-af2c-6acf223a5f7a.png)](https://user-images.githubusercontent.com/81607668/127727503-9d9e7a25-93cb-4f95-8bd0-20b87cb4b459.png)

give it a try from [here](https://8weeksqlchallenge.com/case-study-1/)

## Business Task

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

Danny has provided you 3 different datasets for this case study:

#### Table 1: sales 

| column        | type       |    
| ------------- | ---------- |
| customer_id   | VARCHAR(l) |
| order_date    | DATE       |
| product_id    | INTEGER    |

#### Table 2: menu 

| column       | type       |    
| ------------ | ---------- |
| product_id   | INTEGER    |
| product_name | VARCHAR(5) |
| price        | INTEGER    |

#### Table 3: members 

| column        | type       |    
| ------------- | ---------- |
| customer_id   | VARCHAR(l) |
| join_date     | TIMESTAMP  |

## Case Study Questions


#### 1. What is the total amount each customer spent at the restaurant?

```sql
SELECT customer_id, sum(price) as tot_amount_spent
FROM menu m
INNER JOIN sales s
ON s.product_id = m.product_id
GROUP BY customer_id
```
##### Asnwer:

| customer_id | tot_amount_spent  |    
| ----------- | ----------------- |
| A           | 76                |
| B           | 74                |
| C           | 36                |


#### 2. How many days has each customer visited the restaurant?
```sql
SELECT customer_id, COUNT(DISTINCT order_date) AS times_visited
FROM sales
GROUP BY customer_id
```
##### Asnwer:

| customer_id | times_visited  |    
| ----------- | -------------- |
| A           | 4              |
| B           | 6              |
| C           | 2              |


#### 3. What was the first item from the menu purchased by each customer?

```sql
with rank_cte as
(
SELECT customer_id, product_name, order_date, 
DENSE_RANK() over(PARTITION BY customer_id order by order_date) as rnk
from sales s
inner join menu m
on s.product_id = m.product_id
group by customer_id, product_name, order_date
)
SELECT customer_id, product_name
from rank_cte
where rnk = 1
```

##### Asnwer:

| customer_id | product_name   |    
| ----------- | -------------- |
| A           | curry          |
| A           | sushi          |
| B           | curry          |
| C           | ramen          |


#### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

```sql
SELECT TOP 1 product_name, count(s.product_id) as times_it_was_purchased
from sales s
inner join menu m
on s.product_id = m.product_id
group by product_name
order by times_it_was_purchased desc
```
##### Asnwer:

| product_name | times_it_was_purchased |    
| ------------ | ---------------------- |
| ramen        | 8                      |



#### 5. Which item was the most popular for each customer?
```sql 
with cte_1 as
(SELECT s.customer_id, m.product_name, count(s.product_id) as order_count,
RANK() OVER(PARTITION BY s.customer_id order by count(s.product_id) desc) as rnk
from sales s
inner join menu m
on s.product_id = m.product_id
group by product_name, customer_id
)
select customer_id, product_name, order_count
from cte_1
where rnk = 1
```

##### Asnwer:

| customer_id | product_name    | order_count |   
| ----------- | --------------- | ----------- |
| A           | ramen           | 3           |
| B           | curry           | 2           |
| B           | ramen           | 2           |
| B           | sushi           | 2           |
| C           | ramen           | 3           |


#### 6. Which item was purchased first by the customer after they became a member?

```sql
WITH cte_1 AS 
(
   SELECT s.customer_id, m.join_date, s.order_date, s.product_id,
      DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date) AS rank
   FROM sales AS s
   JOIN members AS m
      ON s.customer_id = m.customer_id
   WHERE s.order_date >= m.join_date
)

SELECT s.customer_id, s.order_date, m2.product_name 
FROM cte_1 AS s
JOIN menu AS m2
   ON s.product_id = m2.product_id
   group by s.customer_id, s.order_date, m2.product_name, rank
HAVING rank = 1
```

##### Asnwer:

| customer_id | order_date  | product_name |   
| ----------- | ----------- | ------------ |
| A           | 2021-01-07  | curry        |
| B           | 2021-01-11  | sushi        |


#### 7. Which item was purchased just before the customer became a member?

```sql
WITH cte_1 AS 
(
   SELECT s.customer_id, m.join_date, s.order_date, s.product_id,
      DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date desc) AS rank
   FROM sales AS s
   JOIN members AS m
      ON s.customer_id = m.customer_id
   WHERE s.order_date < m.join_date
)

SELECT s.customer_id, s.order_date, m2.product_name 
FROM cte_1 AS s
JOIN menu AS m2
   ON s.product_id = m2.product_id
   group by s.customer_id, s.order_date, m2.product_name, rank
HAVING rank = 1

```

##### Asnwer:

| customer_id | order_date  | product_name |   
| ----------- | ----------- | ------------ |
| A           | 2021-01-01  | curry        |
| A           | 2021-01-01  | sushi        |
| B           | 2021-01-04  | sushi        |


#### 8. What is the total items and amount spent for each member before they became a member?


#### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?


#### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

