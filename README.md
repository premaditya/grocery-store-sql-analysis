# Grocery Store SQL Analysis

A relational database for a grocery store, built and analyzed in MySQL. It has 7 linked tables and about 30 queries that answer business questions on top customers, best-selling products, monthly sales trends, and supplier and employee performance.

**Tools:** MySQL, MySQL Workbench
**SQL concepts used:** joins, aggregations (`SUM`, `AVG`, `COUNT`), `GROUP BY`, subqueries, date functions, `IF` conditions, primary and foreign keys

---

## Objectives

- Design and build a relational database for a grocery store
- Import and manage structured data with SQL
- Analyze customer buying behavior and identify high-value customers
- Evaluate product performance by sales volume and revenue
- Study sales trends over time
- Measure supplier and employee contribution to revenue

---

## Repository Contents

| File / Folder | What it is |
|---|---|
| `Grocery Store.sql` | Creates the `grocery` database and its 7 tables, followed by all the analysis queries |
| `datasets/` | The source data for the tables |
| `presentation/` | Project presentation with the ER diagram and screenshots of query results |

---

## Database Schema

| Table | Description |
|---|---|
| `supplier` | Supplier ID, name and address |
| `categories` | Product category ID and name |
| `employees` | Employee ID, name and hire date |
| `customers` | Customer ID, name and address |
| `products` | Product ID, name, price, linked to `supplier` and `categories` |
| `orders` | Order ID and date, linked to `customers` and `employees` |
| `order_details` | Products in each order with quantity, unit price and total price |

Relationships: each product belongs to one supplier and one category. Each order belongs to one customer and is handled by one employee. Each order has one or more rows in `order_details`, which link back to products. Foreign keys use `ON UPDATE CASCADE ON DELETE CASCADE`.

---

## Business Questions Answered

**Customers**
- How many unique customers placed orders?
- Which customers placed the most orders?
- What are the total and average purchase values per customer?
- Who are the top 5 customers by total spend?

**Products and categories**
- How many products exist in each category, and what is the average price per category?
- Which products have the highest sales volume by quantity?
- What is the total revenue per product?
- How do sales vary by category and supplier?

**Orders and sales trends**
- How many orders were placed in total, and what is the average order value?
- On which dates were the most orders placed?
- What are the monthly trends in order volume and revenue?
- How do order patterns differ between weekdays and weekends?

**Suppliers**
- How many suppliers are there, and which supplies the most products?
- What is the average product price per supplier?
- Which suppliers contribute the most revenue?

**Employees**
- How many employees processed orders, and who handled the most?
- What is the total sales value and average order value handled by each employee?

**Pricing**
- What is the relationship between quantity ordered and total price?
- Does the unit price vary across products and orders?

---

## Sample Queries

**Top 5 customers by total spend**
```sql
SELECT c.cust_id, c.cust_name, SUM(od.total_price) AS Total_Spent
FROM customers c
JOIN orders o ON c.cust_id = o.cust_id
JOIN order_details od ON o.ord_id = od.ord_id
GROUP BY c.cust_id, c.cust_name
ORDER BY Total_Spent DESC
LIMIT 5;
```

**Monthly order volume and revenue**
```sql
SELECT MONTH(order_date) AS month,
       COUNT(DISTINCT o.ord_id) AS total_orders,
       SUM(od.total_price) AS revenue
FROM orders o
JOIN order_details od ON o.ord_id = od.ord_id
GROUP BY MONTH(order_date)
ORDER BY month;
```

**Average value per order (subquery)**
```sql
SELECT AVG(order_total) AS avg_order_value
FROM (
  SELECT ord_id, SUM(total_price) AS order_total
  FROM order_details
  GROUP BY ord_id
) t;
```

**Weekday vs weekend orders**
```sql
SELECT IF(DAYOFWEEK(order_date) IN (1,7), 'Weekend', 'Weekday') AS day_type,
       COUNT(*) AS total_orders
FROM orders
GROUP BY day_type;
```

---

## Key Insights

- A small group of customers and products contributes a large share of total revenue.
- Quantity ordered and total price are directly proportional.
- Unit price does not vary within a product, because each product has a fixed price.
- Sales patterns over time point to opportunities for better inventory and demand planning.
- Supplier and employee performance both have a clear effect on revenue.

---

## How to Run

1. Open MySQL Workbench and connect to your local server.
2. Run the table-creation part of `Grocery Store.sql` (the `CREATE DATABASE` and `CREATE TABLE` statements at the top).
3. Import the files from `datasets/` into the matching tables. Import in this order because of the foreign keys: `supplier`, `categories`, `employees`, `customers`, `products`, `orders`, `order_details`.
4. Run the analysis queries from the rest of `Grocery Store.sql`.

---

## Challenges and Learnings

- Managing foreign key constraints during data import taught me to load tables in dependency order.
- Learned to use the right data types, especially for dates.
- Practiced writing joins, aggregations and subqueries, and debugging query errors.

---

## Author

**Prem Aditya Dhulipala**
[GitHub](https://github.com/premaditya) · [LinkedIn](https://www.linkedin.com/in/prem-aditya-dhulipala-627a43268/)
