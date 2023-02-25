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
| unique_orders |  
| ----------- | 
| 10          | 


| runner_id | successful_orders |
| -------- | --------------- |
| 1	| 4 |
| 2 |	3 |
| 3 |	1 |




