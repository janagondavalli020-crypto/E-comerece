# E-comerece


## Project 3: E-Commerce Orders Database

Customers, products, and orders — a great intro to joins and aggregation.

```sql
-- ===== SCHEMA =====
DROP TABLE IF EXISTS order_items;
DROP TABLE IF EXISTS orders;
DROP TABLE IF EXISTS products;
DROP TABLE IF EXISTS customers;
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),
    city VARCHAR(50),
    signup_date DATE
);

CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10,2)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

-- ===== SAMPLE DATA =====
INSERT INTO customers VALUES
(1, 'Ananya Das', 'Bengaluru', '2023-01-10'),
(2, 'Vikram Singh', 'Mumbai', '2023-02-14'),
(3, 'Divya Menon', 'Chennai', '2023-03-05');

INSERT INTO products VALUES
(1, 'Wireless Mouse', 'Electronics', 599.00),
(2, 'Notebook Set', 'Stationery', 199.00),
(3, 'Bluetooth Speaker', 'Electronics', 1499.00),
(4, 'Desk Lamp', 'Home', 899.00);

INSERT INTO orders VALUES
(1, 1, '2023-06-01'),
(2, 2, '2023-06-03'),
(3, 1, '2023-06-10'),
(4, 3, '2023-06-15');

INSERT INTO order_items VALUES
(1, 1, 1, 2),
(2, 1, 2, 3),
(3, 2, 3, 1),
(4, 3, 4, 1),
(5, 4, 1, 1),
(6, 4, 3, 2);

-- ===== PRACTICE QUERIES =====

-- 1. Total amount spent by each customer
SELECT c.name, SUM(p.price * oi.quantity) AS total_spent
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN order_items oi ON o.order_id = oi.order_id
JOIN products p ON oi.product_id = p.product_id
GROUP BY c.name
ORDER BY total_spent DESC;

-- 2. Best-selling product (by quantity)
SELECT p.product_name, SUM(oi.quantity) AS total_sold
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
GROUP BY p.product_name
ORDER BY total_sold DESC
LIMIT 1;

-- 3. Revenue by category
SELECT p.category, SUM(p.price * oi.quantity) AS revenue
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
GROUP BY p.category;

-- 4. Customers who never placed an order
SELECT c.name
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;

-- 5. Orders placed in June with item count
SELECT o.order_id, c.name, COUNT(oi.order_item_id) AS num_items
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN order_items oi ON o.order_id = oi.order_id
WHERE o.order_date BETWEEN '2023-06-01' AND '2023-06-30'
GROUP BY o.order_id, c.name;
