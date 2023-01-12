--1. What is the total amount each customer spent at the restaurant?

SELECT customer_id, sum(price) as tot_amount_spent
FROM menu m
INNER JOIN sales s
ON s.product_id = m.product_id
GROUP BY customer_id

--2. How many days has each customer visited the restaurant?

SELECT customer_id, COUNT(DISTINCT order_date) AS times_visited
FROM sales
GROUP BY customer_id

--3. What was the first item from the menu purchased by each customer?

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

--4. What is the most purchased item on the menu and how many times was it purchased by all customers?
SELECT TOP 1 product_name, count(s.product_id) as times_it_was_purchased
from sales s
inner join menu m
on s.product_id = m.product_id
group by product_name
order by times_it_was_purchased desc

--5. Which item was the most popular for each customer?

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


--6. Which item was purchased first by the customer after they became a member?

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

--7. Which item was purchased just before the customer became a member?

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

--8. What is the total items and amount spent for each member before they became a member?

select distinct s.customer_id, count(distinct(s.product_id)) as total_items, sum(m.price) as total_amount_spent
from sales s
inner join menu m
on s.product_id = m.product_id
inner join members me
on s.customer_id = me.customer_id
where s.order_date < me.join_date
group by s.customer_id


--9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
with points as
(SELECT *, 
CASE WHEN product_name = 'sushi' then price*20 ELSE price*10 END as points
from menu m)

SELECT s.customer_id, SUM(p.points) as total_points
from points as p
join sales s
on s.product_id = p.product_id
group by customer_id

--10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, 
--not just sushi - how many points do customer A and B have at the end of January?
WITH dates AS 
(
   SELECT *, 
      DATEADD(DAY, 6, join_date) AS valid_date, 
      EOMONTH('2021-01-31') AS last_date
   FROM members AS m
)

SELECT d.customer_id,
	SUM(
	CASE WHEN product_name = 'sushi' then price*20
	 WHEN s.order_date BETWEEN d.join_date AND d.valid_date THEN price * 20
	ELSE price*10 END 
	) as total_points
from dates d
join sales s
on d.customer_id = s.customer_id
join menu m
on m.product_id = s.product_id
WHERE s.order_date < d.last_date
group by d.customer_id




