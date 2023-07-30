## E-R Diagram

![Week-1-ER](https://github.com/mirkocoz/8-Week-SQL-Challenge/assets/225798/d4057248-def7-4f21-9db1-91d2430bda59)

## Case Study Questions

### 1. What is the total amount each customer spent at the restaurant?

#### Query  

````sql
SELECT
	s.customer_id AS customer,
  	SUM(m.price) AS total_amount
FROM 
	sales s INNER JOIN menu m ON (s.product_id = m.product_id)
GROUP BY 
	s.customer_id
````
#### Result
|customer|total_amount|
|-----------|------------|
|B|74|
|C|36|
|A|76|

---
### 2. How many days has each customer visited the restaurant?
#### Query  

```sql
SELECT
	customer_id AS customer,
	COUNT(distinct order_date) AS days
FROM 
	dannys_diner.sales AS total_days
GROUP BY
	customer_id
```
#### Result
|customer|days|
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

---
### 6. Which item was purchased first by the customer after they became a member?
#### Query  

```sql
WITH cte_sales AS (
 SELECT
 	ROW_NUMBER() OVER(PARTITION BY s.customer_id ORDER BY s.order_date) AS number,
 	s.customer_id AS customer,
 	p.product_name AS item 
 FROM 
 	members m INNER JOIN sales s on (m.customer_id = s.customer_id)
 	INNER JOIN menu p ON (s.product_id = p.product_id)
WHERE 
	s.order_date  > m.join_date 
)
SELECT
	customer,
	item
FROM
	cte_sales
WHERE 
	number = 1	
	
```
#### Result
|customer|item|
|--------|----|
|A|ramen|
|B|sushi|

---
### 7. Which item was purchased just before the customer became a member?
#### Query  

```sql
WITH cte_sales AS (	
SELECT
 	DENSE_RANK  () OVER(PARTITION BY s.customer_id ORDER BY s.order_date DESC) AS rank,
 	s.customer_id AS customer,
 	p.product_name AS item
 FROM 
 	sales s LEFT JOIN members m  ON (s.customer_id = m.customer_id)  
 	INNER JOIN menu p ON (s.product_id = p.product_id)
WHERE 
	s.order_date  < m.join_date 
)
SELECT
	customer,
	item
FROM
	cte_sales
WHERE 
	rank = 1	
	
```
#### Result
|customer|item|
|--------|----|
|A|sushi|
|A|curry|
|B|sushi|

---
### 8. What is the total items and amount spent for each member before they became a member?
#### Query  

```sql
SELECT
	s.customer_id AS customer,
 	COUNT(s.product_id) AS total_items,
 	SUM(p.price) AS amount_spent
 FROM 
 	members m INNER JOIN  sales s on (m.customer_id = s.customer_id)
 	INNER JOIN menu p ON (s.product_id = p.product_id)
WHERE 
	s.order_date < m.join_date 
GROUP BY 
	s.customer_id	
	
```
#### Result
|customer|total_items|amount_spent|
|--------|-----------|------------|
|B|3|40|
|A|2|25|

---
### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
#### Query  

```sql
SELECT
	s.customer_id AS customer,
	SUM(
	CASE 
		WHEN p.product_name ='sushi' THEN 20 * p.price
	ELSE
		10 * p.price
	END 
	) AS points		
 FROM 
 	sales s INNER JOIN menu p ON (s.product_id = p.product_id)
	 
GROUP BY 
	s.customer_id
	
```
#### Result
|customer|points|
|--------|------|
|B|940|
|C|360|
|A|860|


-- 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
SELECT
	
FROM 
 	members m INNER JOIN  sales s on (m.customer_id = s.customer_id)
 	INNER JOIN menu p ON (s.product_id = p.product_id)
WHERE
	s.customer_id IN ('A','B')
	AND s.order_date  BETWEEN m.join_date AND m.join_date + INTERVAL '6 days'

	
FROM 
 	members m INNER JOIN  sales s on (m.customer_id = s.customer_id)
 	INNER JOIN menu p ON (s.product_id = p.product_id)
WHERE
	s.customer_id IN ('A','B')
	AND s.order_date  BETWEEN m.join_date + INTERVAL '7 days' AND '2021-01-31'	
	
	
	
SELECT '2021-01-01'::DATE + INTERVAL '6 days'

## Bonus Questions
---
### Join All The Things

The following questions are related creating basic data tables that Danny and his team can use to quickly derive insights without needing to join the underlying tables using SQL.

Recreate the following table output using the available data: 

[image](https://github.com/mirkocoz/8-Week-SQL-Challenge/assets/225798/6f98796e-7456-48d6-8d46-1a1346c6ebaf

### Query

```sql
SELECT
	s.customer_id, 
	s.order_date,
	p.product_name,
	p.price,
	CASE 
		WHEN s.order_date >= m.join_date THEN 'Y'
		WHEN s.order_date < m.join_date  THEN 'N'
		WHEN m.join_date IS NULL THEN 'N'
	END AS member
FROM 
 	sales s LEFT JOIN members m ON (s.customer_id = m.customer_id)
 	INNER JOIN menu p ON (s.product_id = p.product_id)
ORDER BY 
	s.customer_id, s.order_date, p.product_name 
```	

### Rank All The Things

Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values
 for the records when customers are not yet part of the loyalty program 




 
WITH cte_sales AS 
(
	SELECT
		s.customer_id, 
		s.order_date,
		p.product_name,
		p.price,
		CASE 
			WHEN s.order_date >= m.join_date THEN 'Y'
			WHEN s.order_date < m.join_date  THEN 'N'
			WHEN m.join_date IS NULL THEN 'N'
		END AS member
	FROM 
	 	sales s LEFT JOIN members m ON (s.customer_id = m.customer_id)
	 	INNER JOIN menu p ON (s.product_id = p.product_id)
	ORDER BY 
		s.customer_id, s.order_date, p.product_name	
)
SELECT
	customer_id, 
	order_date,
	product_name,
	price,
	MEMBER,
	CASE 
		WHEN MEMBER='N' THEN NULL
		ELSE RANK() OVER(PARTITION BY customer_id,MEMBER  ORDER BY order_date)  
	END AS ranking
FROM 
	cte_sales
	

	
SELECT
    s.customer_id,
    s.order_date,
    p.product_name,
    p.price,
    CASE 
        WHEN s.order_date >= m.join_date THEN 'Y'
        ELSE 'N'
    END AS member,
    CASE 
        WHEN s.order_date >= m.join_date THEN
            RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date)
        ELSE
            NULL
    END AS ranking
FROM
    sales s
INNER JOIN menu p ON s.product_id = p.product_id
LEFT JOIN members m ON s.customer_id = m.customer_id
ORDER BY
    s.customer_id, s.order_date, p.product_name;
	

















