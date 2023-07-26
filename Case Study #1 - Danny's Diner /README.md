## E-R Diagram

![Week-1-ER](https://github.com/mirkocoz/8-Week-SQL-Challenge/assets/225798/d4057248-def7-4f21-9db1-91d2430bda59)

## Case Study Questions

1. What is the total amount each customer spent at the restaurant?
#### Query  

```SQL
SELECT
	s.customer_id,
  SUM(m.price) AS total_amount
FROM 
	sales s INNER JOIN menu m ON (s.product_id = m.product_id)
GROUP BY 
	s.customer_id
```
#### Result
|customer_id|total_amount|
|-----------|------------|
|B|74|
|C|36|
|A|76|

2. How many days has each customer visited the restaurant?
#### Query  

```SQL
SELECT
	customer_id,
	COUNT(distinct order_date)
FROM 
	dannys_diner.sales AS total_days
GROUP BY
	customer_id
```
#### Result
|customer_id|count|
|-----------|-----|
|A|4|
|B|6|
|C|2|



