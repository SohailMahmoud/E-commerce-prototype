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

