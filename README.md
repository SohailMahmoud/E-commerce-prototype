# E-Commerce Database Design & Analysis

This repository contains the documentation for the database design and SQL analysis based on a foundational e-commerce schema.

## 1. Database Design & Schema Creation

This section addresses foundational design, including schema script creation, entity relationship identification, and ERD generation.

### 1.1. Database Schema Script

The final, normalized, and optimized SQL Data Definition Language (DDL) script used to create the database tables (`Categories`, `Products`, `Customers`, `Orders`, `Order_details`).

**File Location:** `schema.sql`

```
CREATE TABLE categories (
  category_id SERIAL PRIMARY KEY,
  category_name VARCHAR(50)
);

CREATE TABLE products (
  product_id SERIAL PRIMARY KEY,
  category_id INTEGER,
  name VARCHAR(50) NOT NULL,
  description VARCHAR(200),
  price NUMERIC(10,2) NOT NULL,
  stock_quantity INTEGER,
  CONSTRAINT fk_product_category 
    FOREIGN KEY (category_id) 
    REFERENCES categories(category_id)
);

CREATE TABLE customers (
  customer_id SERIAL PRIMARY KEY,
  first_name VARCHAR(50),
  last_name VARCHAR(50),
  email VARCHAR(100) UNIQUE NOT NULL,
  password_hash VARCHAR(255)
);

CREATE TABLE orders (
  order_id SERIAL PRIMARY KEY,
  customer_id INTEGER NOT NULL,
  order_date TIMESTAMP,
  total_amount NUMERIC(10,2) NOT NULL,
  CONSTRAINT fk_order_customer 
    FOREIGN KEY (customer_id) 
    REFERENCES customers(customer_id)
);

CREATE TABLE order_details (
  order_detail_id SERIAL PRIMARY KEY,
  order_id INTEGER NOT NULL,
  product_id INTEGER NOT NULL,
  quantity INTEGER NOT NULL,
  unit_price NUMERIC(10,2) NOT NULL,
  CONSTRAINT fk_order_detail_order 
    FOREIGN KEY (order_id) 
    REFERENCES orders(order_id),
  CONSTRAINT fk_order_detail_product 
    FOREIGN KEY (product_id) 
    REFERENCES products(product_id)
);
```

### 1.2. Entity Relationships Identification

This outlines the relationships (one-to-one, one-to-many, many-to-many) between the core entities.

| Relationship | Type | Explanation |
|-------------|------|-------------|
| Customer and Order | `One to many` | `A customer can place many orders, but each order belongs to one customer.` |
| Order and Order_details | `One to many` | `Each order can contain multiple order detail records, but each order detail belongs to only one order.` |
| Product and Order_details | `One to many` | `A product can appear in many order detail records, but each order detail refers to a single product.` |
| Category and Product | `One to many` | `A category can have many products, but each product belongs to one category.` |

### 1.3. The ERD Diagram

The Entity-Relationship Diagram (ERD) visually represents the normalized schema, including primary keys, foreign keys, and cardinalities.

**ERD Image:**
<img width="1279" height="551" alt="e-commerce prototype" src="https://github.com/user-attachments/assets/b52d6597-9d79-41f5-b07a-4a469e0978ef" />


## 2. SQL Query Analysis

This section contains SQL queries developed to retrieve specific business intelligence metrics.

### 2.1. Daily Revenue Report

**Goal:** Write an SQL query to generate a daily report of the total revenue for a specific date.

**Parameters:** Specific date (e.g., `'2025-11-18'`)

**Example query**:
```
SELECT 
    order_date, 
    SUM(total_amount) AS daily_revenue
FROM 
    "order"
WHERE 
    order_date = <SPECIFIC_DATE>
GROUP BY 
    order_date;
```

### 2.2. Top-Selling Products Report

**Goal:** Write an SQL query to generate a monthly report of the top-selling products in a given month.

**Parameters:** Specific month and year (e.g., `2025-11`)

**Example query**:
```
SELECT 
    product_id,
    SUM(quantity) AS total_units_sold
FROM 
    "order"
JOIN 
    order_details ON "order".order_id = order_details.order_id
WHERE 
    EXTRACT(MONTH FROM order_date) = <MONTH_NUMBER>
    AND EXTRACT(YEAR FROM order_date) = <YEAR_NUMBER>
GROUP BY 
    product_id
ORDER BY 
    total_units_sold DESC
LIMIT 
    <N>;
```

### 2.3. High-Value Customer List

**Goal:** Write a complex SQL query to retrieve customers who have placed orders totaling more than **$500** in the past month. Include customer name and their total order amount.

**Parameters:** Relative to `CURRENT_DATE`

**Example query**:
```
SELECT 
    customer.customer_id, 
    customer.first_name, 
    customer.last_name, 
    SUM("order".total_amount) AS total_order_amount
FROM 
    customer
JOIN 
    "order" ON customer.customer_id = "order".customer_id
WHERE 
    EXTRACT(MONTH FROM order_date) = <MONTH_NUMBER>
    AND EXTRACT(YEAR FROM order_date) = <YEAR_NUMBER>
GROUP BY 
    customer.customer_id, customer.first_name, customer.last_name
HAVING 
    SUM("order".total_amount) >= 500
ORDER BY 
    total_order_amount DESC
LIMIT 
    <N>;
```

## 3. Denormalization Mechanism

This section explains how denormalization could be applied to improve read performance for frequently accessed data.

### 3.1. Denormalization on Customer and Order Entities

**Goal:** Explain a denormalization mechanism on the `Customer` and `Order` entities.











