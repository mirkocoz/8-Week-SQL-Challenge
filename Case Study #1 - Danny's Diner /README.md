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


