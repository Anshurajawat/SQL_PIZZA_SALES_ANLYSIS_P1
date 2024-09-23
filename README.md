# pizza Sales Analysis SQL Project

## Project Overview

**Project Title**: pizza sales analysis  
**Level**: intermediate 
**Database**: `papajohns`

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze pizza sales data. The project involves setting up a pizza sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries. This project is ideal for those who are starting their journey in data analysis and want to build a solid foundation in SQL.

## Objectives

1. **Set up a pizza sales database**: Create and populate a pizza sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `papajohns`.
- 
- **Table Creations**: There are four csv data have given- pizza_types,pizzas,orders,order_details.So we created  four tables with names `pizza_types`,`pizzas`,`orders`,`order_details` to store the needed data. The `pizza_types` table structure includes columns for pizza_type_id ,name,category,incredients.The `pizzas` table struicture include columns for pizza_id,pizza_type_id,size,price. The `orders` table structure include columns for order_id,date,time. The `order_details`table structure include columns for order_details_id,order_id,pizza_id,quantity.


```sql
CREATE DATABASE papajohns;

CREATE TABLE pizza_types
(
  pizza_type_id varchar(20),
name varchar(70),
category varchar(20),
incredients varchar(200)
);
```
```sql
CREATE TABLE pizzas
(
  pizza_id varchar(20),
pizza_type_id varchar(20),
size varchar(2),
price float(10)
);
```
```sql

CREATE TABLE order-details
(
  order_details_id int,
order_id int,
pizza_id varchar(20),
quantity int
);
```
```sql

CREATE TABLE orders
(
  order_id int,
date date,
time time,
);
```



### 2. Data Exploration & Cleaning

- **Record Count**: Determine the total number of records in all the dataset.
- **Customer Count**: Find out how many unique customers are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.

```sql
SELECT COUNT(*) FROM pizza_types;
SELECT COUNT(*) FROM pizzas;
SELECT COUNT(*) FROM orders;
SELECT COUNT(*) FROM order_details;

SELECT * FROM pizza_types
WHERE 
    pizza_type_id IS NULL OR name IS NULL OR category IS NULL OR 
    incredients IS NULL;

DELETE FROM pizza_types
WHERE 
   pizza_type_id IS NULL OR name IS NULL OR category IS NULL OR 
    incredients IS NULL;

SELECT * FROM pizzas
WHERE 
    pizza_type_id IS NULL OR pizza_id IS NULL OR price IS NULL OR 
    size IS NULL;

DELETE FROM pizzas
WHERE 
    pizza_type_id IS NULL OR pizza_id IS NULL OR price IS NULL OR 
    size IS NULL;

SELECT * FROM orders
WHERE 
    order_id IS NULL OR date IS NULL OR time IS NULL;
  

DELETE FROM pizzas
WHERE 
  order_id IS NULL OR date IS NULL OR time IS NULL;

SELECT * FROM order_details
WHERE 
    order_details_id IS NULL OR pizza_id IS NULL OR order_id IS NULL OR 
    quantity IS NULL;

DELETE FROM pizzas
WHERE 
   order_details_id IS NULL OR pizza_id IS NULL OR order_id IS NULL OR 
    quantity IS NULL;

```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Retrieve the total number of orders placed.**:
```sql

SELECT 
    COUNT(ORDER_ID) AS TOTAL_NO_OF_ORDER_PLACED
FROM
    ORDERS;
    
```

2. **Calculate the total revenue generated from pizza sales**:
```sql
SELECT 
    ROUND(SUM(PIZZAS.PRICE * ORDERDETAILS.QUANTITY),
            2) AS TOTAL_N0_REVENUE_GENERATED
FROM
    PIZZAS
        JOIN
    ORDERDETAILS ON PIZZAS.PIZZA_ID = ORDERDETAILS.PIZZA_ID;
```

3. **Identify the highest-priced pizza**:
```sql
SELECT 
    PIZZATYPES.NAME, PIZZAS.PRICE
FROM
    PIZZATYPES
        JOIN
    PIZZAS ON PIZZATYPES.PIZZA_TYPE_ID = PIZZAS.PIZZA_TYPE_ID
ORDER BY PIZZAS.PRICE DESC
LIMIT 1;

```

4. ** Identify the most common pizza size ordered..**:
```sql
SELECT 
    PIZZAS.SIZE, COUNT(ORDERDETAILS.QUANTITY) AS TOTAL_QUANTITY
FROM
    PIZZAS
        JOIN
    ORDERDETAILS ON PIZZAS.PIZZA_ID = ORDERDETAILS.PIZZA_ID
GROUP BY PIZZAS.SIZE
ORDER BY TOTAL_QUANTITY DESC
LIMIT 1;
```

5. **List the top 5 most ordered pizza types along with their quantities.
.**:
```sql
SELECT 
    (PIZZAS.PIZZA_TYPE_ID) AS PIZZA_TYPES,
    SUM(ORDERDETAILS.QUANTITY) AS QUANTITY
FROM
    PIZZAS
        JOIN
    orderdetails ON PIZZAS.PIZZA_ID = ORDERDETAILS.PIZZA_ID
GROUP BY PIZZAS.PIZZA_TYPE_ID
ORDER BY QUANTITY DESC
LIMIT 5;
```

6. **Join the necessary tables to find the total quantity of each pizza category ordered..**:
```sql
SELECT 
    (PIZZATYPES.CATEGORY) AS PIZZA_CATEGORY,
    SUM(ORDERDETAILS.QUANTITY) AS QUANTITY
FROM
    PIZZATYPES
        JOIN
    PIZZAS ON PIZZATYPES.PIZZA_TYPE_ID = PIZZAS.PIZZA_TYPE_ID
        JOIN
    ORDERDETAILS ON ORDERDETAILS.PIZZA_ID = PIZZAS.PIZZA_ID
GROUP BY PIZZA_CATEGORY
ORDER BY QUANTITY DESC;
```

7. **Determine the distribution of orders by hour of the day.**:
```sql
SELECT 
    HOUR(ORDER_TIME) AS HOUR, COUNT(ORDER_ID) AS TOATL_ORDERS
FROM
    ORDERS
GROUP BY HOUR
ORDER BY HOUR ASC;

```

8. **Join relevant tables to find the category-wise distribution of pizzas. **:
```sql
SELECT 
    CATEGORY, COUNT(NAME) AS DISTRIBUTION
FROM
    PIZZATYPES
GROUP BY CATEGORY;

```

9. **Group the orders by date and calculate the average number of pizzas ordered per day..**:
```sql
SELECT 
    ROUND(AVG(ORDER_QUANTITY), 0) AS AVERAGE_ORDERS
FROM
    (SELECT 
        (ORDERS.ORDER_DATE) AS DATE,
            SUM(ORDERDETAILS.QUANTITY) AS ORDER_QUANTITY
    FROM
        ORDERS
    JOIN ORDERDETAILS ON ORDERS.ORDER_ID = orderdetails.ORDER_ID
    GROUP BY DATE) AS TOTAL_ORDER_QUANTITY;
    
```

10. **Determine the top 3 most ordered pizza types based on revenue.**:
```sql
SELECT 
    (PIZZATYPES.NAME) AS PIZZA_NAME,
    ROUND(SUM((PIZZAS.PRICE) * (orderdetails.QUANTITY)),
            2) AS REVENUE
FROM
    PIZZATYPES
        JOIN
    PIZZAS ON PIZZATYPES.PIZZA_TYPE_ID = PIZZAS.PIZZA_TYPE_ID
        JOIN
    orderdetails ON PIZZAS.PIZZA_ID = ORDERDETAILS.PIZZA_ID
GROUP BY PIZZA_NAME
ORDER BY REVENUE DESC
LIMIT 3;
```
10. **Calculate the percentage contribution of each pizza type to total revenue.**:
```sql
SELECT (PIZZAS.PIZZA_TYPE_ID) AS PIZZA_TYPE,ROUND((SUM((PIZZAS.PRICE)*(orderdetails.QUANTITY))/(SELECT 
    ROUND(SUM(PIZZAS.PRICE * ORDERDETAILS.QUANTITY),
            2) AS TOTAL_N0_REVENUE_GENERATED
FROM
    PIZZAS
        JOIN
    ORDERDETAILS ON PIZZAS.PIZZA_ID = ORDERDETAILS.PIZZA_ID))*100,2) AS PERCENTAGE_CONTRI
    FROM PIZZAS JOIN ORDERDETAILS
    ON PIZZAS.PIZZA_ID=orderdetails.PIZZA_ID
    GROUP BY PIZZA_TYPE;
```
10. **Analyze the cumulative revenue generated over time.**:
```sql
SELECT ORDER_DATE , SUM(REVENUE) OVER (ORDER BY ORDER_DATE) AS CUM_REVENUE FROM
(SELECT (ORDERS.ORDER_DATE) AS ORDER_DATE,ROUND(SUM((ORDERDETAILS.QUANTITY)*(PIZZAS.PRICE)),3)AS REVENUE
FROM ORDERS JOIN ORDERDETAILS
ON ORDERS.ORDER_ID=ORDERDETAILS.ORDER_ID
JOIN PIZZAS
ON PIZZAS.PIZZA_ID=ORDERDETAILS.PIZZA_ID
GROUP BY ORDER_DATE ) AS SALES;
```
10. **Determine the top 3 most ordered pizza types based on revenue for each pizza category.**:
```sql
SELECT CATEGORY , NAME,REVENUE, 
RANK() OVER (PARTITION BY CATEGORY ORDER BY NAME DESC) AS RNK FROM
(SELECT PIZZATYPES.CATEGORY,PIZZATYPES.NAME,ROUND(SUM((ORDERDETAILS.QUANTITY)*(PIZZAS.PRICE)),2) AS REVENUE
FROM PIZZATYPES JOIN PIZZAS
ON PIZZATYPES.PIZZA_TYPE_ID=PIZZAS.PIZZA_TYPE_ID
JOIN ORDERDETAILS
ON ORDERDETAILS.PIZZA_ID=PIZZAS.PIZZA_ID
GROUP BY PIZZATYPES.CATEGORY,PIZZATYPES.NAME) AS A;
```
## Findings

- **Customer Demographics**: The dataset includes customers from various taste groups, with sales distributed across different categories such as chicken and veg etc.
- **Revenue **: Monthly analysis shows variations in sales, helping identify peak seasons.
- **Customer Insights**: The analysis identifies the top-spending customers and the most popular pizza categories.

## Reports

- **Sales Summary**: A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis**: Insights into sales trends across different date and shifts.
- **Customer Insights**: Reports on top customers and unique customer counts per category.

## Conclusion

This project serves as a comprehensive introduction to SQL for data analysts, covering database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding sales patterns, customer behavior, and product performance.


