# mysql-project-
✅ Complex MySQL Project
Online Retail Management System

1️⃣ Project Overview

The Online Retail Management System manages:

Customers

Products

Orders

Payments

Inventory

Suppliers

Employees

Sales & Revenue reports
3️⃣ Database Schema (Tables)
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE,
    password VARCHAR(255),
    role ENUM('customer','admin','employee'),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

🔹 Products
CREATE TABLE products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    product_name VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10,2),
    stock INT,
    status ENUM('available','out_of_stock'),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
🔹 Customers
CREATE TABLE customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    address TEXT,
    phone VARCHAR(15),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
🔹 Orders
CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    total_amount DECIMAL(10,2),
    status ENUM('pending','paid','shipped','delivered','cancelled'),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
🔹 Order Items
CREATE TABLE order_items (
    order_item_id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    price DECIMAL(10,2),
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
🔹 Payments
CREATE TABLE payments (
    payment_id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    payment_method ENUM('UPI','Card','NetBanking','COD'),
    payment_status ENUM('success','failed'),
    payment_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
);
4️⃣ Advanced Features
🔸 Trigger – Auto Update Stock After Order
CREATE TRIGGER update_stock_after_order
AFTER INSERT ON order_items
FOR EACH ROW
UPDATE products
SET stock = stock - NEW.quantity
WHERE product_id = NEW.product_id;
🔸 Stored Procedure – Place Order
DELIMITER $$

CREATE PROCEDURE place_order(
    IN cust_id INT,
    IN total DECIMAL(10,2)
)
BEGIN
    INSERT INTO orders (customer_id, total_amount, status)
    VALUES (cust_id, total, 'pending');
END$$

DELIMITER ;
🔸 Function – Calculate Total Order Value
CREATE FUNCTION order_total(oid INT)
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE total DECIMAL(10,2);
    SELECT SUM(quantity * price)
    INTO total
    FROM order_items
    WHERE order_id = oid;
    RETURN total;
END;
5️⃣ Complex SQL Queries
✅ Top Selling Products
SELECT p.product_name, SUM(oi.quantity) AS total_sold
FROM products p
JOIN order_items oi ON p.product_id = oi.product_id
GROUP BY p.product_name
ORDER BY total_sold DESC;
✅ Monthly Sales Report
SELECT MONTH(order_date) AS month, SUM(total_amount) AS revenue
FROM orders
WHERE status = 'paid'
GROUP BY MONTH(order_date);
✅ Customers With Highest Purchases
SELECT c.customer_id, u.name, SUM(o.total_amount) AS total_spent
FROM customers c
JOIN users u ON c.user_id = u.user_id
JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id
ORDER BY total_spent DESC;

 Tools Used

MySQL 8+

MySQL Workbench

SQL (DDL, DML, Triggers, Procedures)
