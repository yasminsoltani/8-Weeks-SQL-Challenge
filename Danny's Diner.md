# 🍽 Case Study #1 - Danny's Diner

![danny's diner](https://user-images.githubusercontent.com/103854541/211166454-6af0b127-e0d5-4c20-af2c-6acf223a5f7a.png)

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
```
SELECT customer_id, sum(price) as tot_amount_spent
FROM menu m
INNER JOIN sales s
ON s.product_id = m.product_id
GROUP BY customer_id
```

#### 2. How many days has each customer visited the restaurant?


#### 3. What was the first item from the menu purchased by each customer?


#### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?


#### 5. Which item was the most popular for each customer?


#### 6. Which item was purchased first by the customer after they became a member?


#### 7. Which item was purchased just before the customer became a member?


#### 8. What is the total items and amount spent for each member before they became a member?


#### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?


#### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

