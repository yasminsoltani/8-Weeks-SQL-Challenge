# 🍕 Case Study #2 - Pizza Runner
## B. Runner and Customer Experience

#### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

```sql
SELECT datepart(week, registration_date) AS registration_week, COUNT(runner_id) AS num_runners
FROM runners
GROUP BY datepart(week, registration_date)
```

#### Asnwer:

| registration_week | num_runners
| ------ | ------ |
| 1	| 1
| 2	| 2
| 3	| 1


#### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

```sql
SELECT r.runner_id, AVG(DATEDIFF(MINUTE,c.order_time,r.pickup_time)) AS avg_minutes
FROM #runner_orders r
JOIN #customer_orders c
ON r.order_id = c.order_id
WHERE r.pickup_time != ''
GROUP BY runner_id
```


#### Asnwer:

|  runner_id | avg_minutes |
| ------ | ------ |
| 1	| 15
| 2 |	24
| 3 |	10


#### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?

```sql
with prepare_time AS
(
select c.order_id,count(pizza_id) as pizza_tot, DATEDIFF(MINUTE,c.order_time,r.pickup_time) as prepare_time_minutes
from #runner_orders r
join #customer_orders c
on r.order_id = c.order_id
where r.pickup_time != ''
group by c.order_id, DATEDIFF(MINUTE,c.order_time,r.pickup_time)
)

select pizza_tot, avg(prepare_time_minutes) preparation_time
from prepare_time
group by pizza_tot
```

#### Asnwer:

|  pizza_tot | preparation_time |
| ------ | ------ |
| 1	| 12
| 2 |	18
| 3	| 30


#### 4. What was the average distance travelled for each customer?

```sql
select c.customer_id, avg(cast(distance as float)) as avg_distance
from #runner_orders r
join #customer_orders c
on r.order_id = c.order_id
where r.distance != ''
group by  customer_id
```

#### Asnwer:

|  customer_id | avg_distance |
| ------ | ------ |
| 101	| 20
| 102	| 16.73
| 103	| 23.4
| 104	| 10
| 105	| 25


#### 5. What was the difference between the longest and shortest delivery times for all orders?

```sql
with cte as(
SELECT order_id, CAST(duration as float) as duration
FROM #runner_orders
WHERE duration != ''
)
SELECT MAX(duration) - MIN(duration) as delivery_difference
FROM cte
```

#### Asnwer:

|  delivery_difference | 
| ------ | 
| 30 |


#### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

```sql
with conversion as 
(SELECT order_id, CAST(duration as float) as duration, cast(distance as float) as distance
FROM #runner_orders
WHERE duration != ''
)
SELECT order_id , AVG(distance/duration * 60) AS avg_speed   
FROM conversion
WHERE distance != 0
GROUP BY order_id
```

#### Asnwer:

|  order_id | avg_speed |
| ------ | ------ |
| 1	| 37.5
| 2	| 44.4
| 3| 	40.2
| 4	| 35.1
| 5	| 40
| 7	| 60
| 8	| 93.6
| 10| 	60




#### 7. What is the successful delivery percentage for each runner?

```sql
SELECT runner_id, 100 * SUM(CASE WHEN duration != 0 THEN 1 ELSE 0 END) / COUNT(order_id) AS successful_percentage
FROM #runner_orders
GROUP BY runner_id
```

#### Asnwer:

|  runner_id | successful_percentage |
| ------ | ------ |
| 1	| 100
| 2	| 75
| 3	| 50


#### For solution C. Ingredients Optimization [here] 
