# 🍕 Case Study #2 - Pizza Runner

<img src="https://user-images.githubusercontent.com/103854541/221377608-4845b975-912e-426c-b39f-394d10332dcf.png" width="500" height="500">


give it a try from [here](https://8weeksqlchallenge.com/case-study-2/) or view my raw SQL solution [here]

## Business Task

Danny was scrolling through his Instagram feed when something really caught his eye - “80s Retro Styling and Pizza Is The Future!”

Danny was sold on the idea and decides to expand his new Pizza Empire; he does so by launching Pizza Runner.

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.

### Entity Relationship Diagram


<img width="600" alt="entity relationship" src="https://user-images.githubusercontent.com/103854541/221377903-0a8d90e2-5377-496d-acfb-5d7de8627eec.png">

### 🧹 Data Cleaning and Transformation

Danny recommends to investigate the data before starting writing SQL queries, since the null values and data types in the customer_orders and runner_orders tables need to be cleaned. 

### ⛏️ Table: customer_orders

By looking at the customer_orders table we notice:

• there are null values as well as empty cells in the ``exclusions`` and ``extras`` columns


<img width="500" alt="customer_orders" src="https://user-images.githubusercontent.com/103854541/221378437-0e1f73f7-4985-4c46-a564-3a4b3bc6554c.png">

Let's go ahead and create a temporary tables called "```#customer_orders```" by removing null values with empty cells. Remember to use this table during the analysis and not the original customer_orders table 

```sql
SELECT 
    order_id,customer_id,pizza_id,
    CASE 
      WHEN exclusions IS NULL OR exclusions LIKE 'null' THEN ''
      ELSE exclusions
      END AS exclusions,
    CASE
      WHEN extras IS NULL OR extras LIKE 'null' THEN ''
      ELSE extras 
      END AS extras,
    order_time
INTO #customer_orders -- #this will be our table of reference throghout the analysis 
FROM customer_orders;
```

This is how our final ```#customer_orders``` table looks like

<img width="500" alt="#customer_orders" src="https://user-images.githubusercontent.com/103854541/221378765-041d9fe6-c4bf-4c46-8d3a-9bf1d62f7f75.png">

### ⛏️ Table: runner_orders

By looking at the runner_orders table we notice:

• there are null values in the ``pickup_time``, ``distance``, ``duration`` and ``cancellation`` columns

• there is "km" in some of the values in the ``distance`` colummn

• there is "minutes" and "mins" in some of the values in the ``duration`` column

• there are empty cells in the ``cancellation`` column 

<img width="500" alt="runner" src="https://user-images.githubusercontent.com/103854541/221379176-dae912a9-ce62-4cb4-b3f6-6a33b758117d.png">

Let's proceed to substitute null values with empty spaces, removing "km","minutes","mins" in the different cells.

```sql
SELECT order_id, runner_id,
  CASE 
    WHEN pickup_time LIKE 'null' THEN ''
    ELSE pickup_time
    END AS pickup_time,
  CASE 
    WHEN distance LIKE 'null' THEN ''
    WHEN distance LIKE '%km' THEN TRIM( 'km' FROM distance)
    ELSE distance 
    END AS distance,
  CASE 
    WHEN duration LIKE 'null' THEN ''
    WHEN duration LIKE '%mins' THEN TRIM('mins' from duration)
    WHEN duration LIKE '%minute' THEN TRIM('minute' from duration)
    WHEN duration LIKE '%minutes' THEN TRIM( 'minutes' FROM duration)
    ELSE duration
    END AS duration,
  CASE 
    WHEN cancellation IS NULL OR cancellation LIKE 'null' THEN ''
    ELSE cancellation
    END AS cancellation
INTO #runner_orders-- #this will be our table of reference throghout the analysis
FROM runner_orders;
```

This is how our final ```#runner_orders``` table looks like

<img width="500" alt="#runners" src="https://user-images.githubusercontent.com/103854541/221379290-062cfe45-f55d-4324-9b52-694272e5c152.png">

Now that our tables are cleaned and ready to use, we can start working to the different questions!

### 📌 Case Study Questions: 

#### A: Pizza Metrics

1. How many pizzas were ordered?
2. How many unique customer orders were made?
3. How many successful orders were delivered by each runner?
4. How many of each type of pizza was delivered?
5. How many Vegetarian and Meatlovers were ordered by each customer?
6. What was the maximum number of pizzas delivered in a single order?
7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
8. How many pizzas were delivered that had both exclusions and extras?
9. What was the total volume of pizzas ordered for each hour of the day?
10. What was the volume of orders for each day of the week?

view my solution to ```Pizza Metrics``` [here](https://github.com/yasminsoltani/8-Weeks-SQL-Challenge/blob/main/Case%20Study%20%232:%20Pizza%20Runner/A.%20Pizza%20Metrics.md)

#### B: Runner and Customer Experience

1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
4. What was the average distance travelled for each customer?
5. What was the difference between the longest and shortest delivery times for all orders?
6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
7. What is the successful delivery percentage for each runner?

view my solution to ```Runner and Customer Experience``` [here](https://github.com/yasminsoltani/8-Weeks-SQL-Challenge/blob/main/Case%20Study%20%232:%20Pizza%20Runner/B.%20Runner%20and%20Customer%20Experience.md)

