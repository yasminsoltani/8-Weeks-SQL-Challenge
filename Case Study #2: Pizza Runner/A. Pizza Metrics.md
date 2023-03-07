# 🍕 Case Study #2 - Pizza Runner
## A. Pizza Metrics

#### 1. How many pizzas were ordered?

```sql
SELECT COUNT(pizza_id) AS total_pizzas_ordered
FROM #customer_orders 
```

#### Asnwer:

| total_pizzas_ordered |  
| ----------- | 
| 14          | 


#### 2. How many unique customer orders were made?

```sql
SELECT COUNT(DISTINCT order_id) AS unique_orders
FROM #customer_orders
```

#### Asnwer:

| unique_orders |  
| ----------- | 
| 10          | 


#### 3. How many successful orders were delivered by each runner?

```sql
SELECT runner_id, COUNT(order_id) AS successful_orders
FROM #runner_orders
WHERE distance != ''
GROUP BY runner_id
```

#### Asnwer:

| runner_id | successful_orders |
| -------- | --------------- |
| 1	| 4 |
| 2 |	3 |
| 3 |	1 |


#### 4. How many of each type of pizza was delivered?

```sql
SELECT CAST(p.pizza_name AS VARCHAR(20)) AS pizza_name, --we need to use type casting since it won't allow to group by pizza_name otherwise
COUNT(c.pizza_id) AS total_pizza_delivered
FROM #customer_orders c
JOIN #runner_orders r
ON c.order_id = r.order_id
JOIN pizza_names p
ON p.pizza_id = c.pizza_id
WHERE distance != ''
GROUP BY CAST(p.pizza_name AS VARCHAR(20))
```


#### Asnwer:

| pizza_name | total_pizza_delivered |
| --------- | ------------------- |
| Meatlovers |	9 |
| Vegetarian	| 3 |


#### 5. How many Vegetarian and Meatlovers were ordered by each customer?

```sql
SELECT c.customer_id, CAST(p.pizza_name AS VARCHAR(20)) AS pizza_name,
COUNT(CAST(p.pizza_name AS VARCHAR(20))) AS total_pizza
FROM #customer_orders c
JOIN pizza_names p                                                    
ON c.pizza_id = p.pizza_id
GROUP BY c.customer_id,CAST(p.pizza_name AS VARCHAR(20))   
order by customer_id
```

#### Asnwer:

| customer_id | pizza_name | total_pizza |
| ---------- | ---------- | ----------- | 
| 101	| Meatlovers	| 2 |
| 101	| Vegetarian	| 1 |
| 102	| Meatlovers	| 2 |
| 102	| Vegetarian	| 1 |
| 103	| Meatlovers	| 3 |
| 103	| Vegetarian	| 1 |
| 104	| Meatlovers	| 3 |
| 105	| Vegetarian	| 1 |


#### 6. What was the maximum number of pizzas delivered in a single order? 

```sql
SELECT TOP 1 c.order_id, COUNT(c.pizza_id) as max_num_pizzas
FROM #customer_orders c
join #runner_orders r
on r.order_id = c.order_id
where r.distance != ''
group by c.order_id
order by max_num_pizzas desc; 
```


#### Asnwer:

| order_id | max_num_pizzas |
| -------- | -------------- |
| 4	 | 3 |


#### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

```sql
SELECT customer_id,
SUM(CASE WHEN exclusions != '' OR extras != '' THEN 1 ELSE 0 END) AS at_least_1_change_pizzas,
SUM(CASE WHEN exclusions = '' AND extras = '' THEN 1 ELSE 0 END) AS unchanged_pizzas
FROM #customer_orders c
join #runner_orders r
on r.order_id = c.order_id
where r.distance != ''
group by customer_id
```

#### Asnwer:

| customer_id | at_least_1_change_pizzas | unchanged_pizzas |
| ----------- | ------------------------ | ----------------- |
| 101 | 0 |	2 |
| 102	| 0	| 3 |
| 103	| 3	| 0 |
| 104	| 2	| 1 |
| 105	| 1	| 0 | 


#### 8. How many pizzas were delivered that had both exclusions and extras?

```sql
SELECT 
SUM(CASE WHEN exclusions != '' AND extras != '' THEN 1 ELSE 0 END) AS pizzas_with_exclusions_and_extras
FROM #customer_orders c
join #runner_orders r
on r.order_id = c.order_id
where r.distance != ''
```

#### Asnwer:

| pizzas_with_exclusions_and_extras | 
| ------------------------------ | 
| 1 |


#### 9. What was the total volume of pizzas ordered for each hour of the day?

```sql
select datepart(HOUR, order_time) as hour, COUNT(order_id) as tot_pizzas
from #customer_orders
group by datepart(HOUR, order_time)
```


#### Asnwer:

| hour | tot_pizzas |
| ---- | ---------- |
| 11	| 1 | 
| 13	| 3 | 
| 18	| 3 | 
| 19	| 1 |
| 21	| 3 |
| 23	| 3 |


#### 10. What was the volume of orders for each day of the week?

```sql
select FORMAT(DATEADD(DAY, 2, order_time),'dddd') AS day_of_week, COUNT(order_id) as tot_pizzas
from #customer_orders
group by FORMAT(DATEADD(DAY, 2, order_time),'dddd') 
```


#### Asnwer:

| day_of_week | tot_pizzas |
| ----------- | --------- | 
| Friday	| 5 | 
| Monday	| 5 |
| Saturday	| 3 |
| Sunday	| 1 |


#### For solution B. Runner and Customer Experience click [here](https://github.com/yasminsoltani/8-Weeks-SQL-Challenge/blob/main/Case%20Study%20%232:%20Pizza%20Runner/B.%20Runner%20and%20Customer%20Experience.md) 
