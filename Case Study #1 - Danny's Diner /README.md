## E-R Diagram

![Week-1-ER](https://github.com/mirkocoz/8-Week-SQL-Challenge/assets/225798/d4057248-def7-4f21-9db1-91d2430bda59)

## Case Study Questions

### 1. What is the total amount each customer spent at the restaurant?

#### Query  

````sql
SELECT
	s.customer_id,
  	SUM(m.price) AS total_amount
FROM 
	sales s INNER JOIN menu m ON (s.product_id = m.product_id)
GROUP BY 
	s.customer_id
````
#### Result
|customer_id|total_amount|
|-----------|------------|
|B|74|
|C|36|
|A|76|

---
### 2. How many days has each customer visited the restaurant?
#### Query  

```sql
SELECT
	customer_id,
	COUNT(distinct order_date) AS days
FROM 
	dannys_diner.sales AS total_days
GROUP BY
	customer_id
```
#### Result
|customer_id|days|
|-----------|-----|
|A|4|
|B|6|
|C|2|

---
### 3. What was the first item from the menu purchased by each customer?
#### Query  

```sql
WITH purchases AS 
(
	SELECT
		ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY  order_date) AS number, 
		customer_id  AS customer,
		p.product_name AS item 
	FROM 
		sales s INNER JOIN menu p ON (s.product_id = p.product_id)
)
SELECT
	customer,
	item
FROM 
	purchases
WHERE 
	number = 1
```
#### Result
|customer|item|
|-----------|------------|
|A|curry|
|B|curry|
|C|ramen|

---
### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
#### Query  

```sql
SELECT
	m.product_name  AS item,
	COUNT(s.product_id) as purchased_times
FROM 
	sales s INNER JOIN menu m on (s.product_id = m.product_id)
GROUP BY 
	m.product_name
ORDER BY  		
	purchased_times desc
LIMIT 1	
	
```
#### Result
|item|purchased_times|
|----|---------------|
|ramen|8|

---
### 5.  Which item was the most popular for each customer?
#### Query  

```sql
WITH consolidate AS
(
	SELECT
		DENSE_RANK () OVER(PARTITION BY s.customer_id ORDER BY COUNT(s.product_id) DESC) AS ranking,
		s.customer_id AS customer,
		m.product_name  AS item,
		COUNT(s.product_id) AS total_sales
	FROM sales s INNER JOIN menu m ON (s.product_id = m.product_id)
	GROUP BY 
		s.customer_id, m.product_name 
)	
SELECT
	customer,
	item
FROM 
	consolidate
WHERE
	ranking = 1
	
```
#### Result
|customer|item|
|--------|----|
|A|ramen|
|B|sushi|
|B|curry|
|B|ramen|
|C|ramen|










