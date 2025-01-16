# -E-Commerce-Sales-Analytics
-- E-Commerce Sales Analytics Project

CREATE DATABASE ecommerce_analytics_v2;
USE ecommerce_analytics_v2;

-- Step 1: Create Tables
CREATE TABLE Customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    join_date DATE NOT NULL,
    country VARCHAR(50) NOT NULL
);

CREATE TABLE Products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    category VARCHAR(50) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    stock_quantity INT NOT NULL
);

CREATE TABLE Orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    order_date DATE NOT NULL,
    total_amount DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
);

CREATE TABLE Order_Details (
    order_detail_id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    total_price DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id),
    FOREIGN KEY (product_id) REFERENCES Products(product_id)
);

CREATE TABLE Suppliers (
    supplier_id INT AUTO_INCREMENT PRIMARY KEY,
    supplier_name VARCHAR(100) NOT NULL,
    contact VARCHAR(100)
);

CREATE TABLE Product_Suppliers (
    product_supplier_id INT AUTO_INCREMENT PRIMARY KEY,
    product_id INT NOT NULL,
    supplier_id INT NOT NULL,
    FOREIGN KEY (product_id) REFERENCES Products(product_id),
    FOREIGN KEY (supplier_id) REFERENCES Suppliers(supplier_id)
);

-- Step 2: Insert Sample Data
-- Customers
INSERT INTO Customers (name, email, join_date, country) VALUES
('Alice Johnson', 'alice@example.com', '2022-01-15', 'USA'),
('Bob Smith', 'bob@example.com', '2022-03-10', 'Canada'),
('Charlie Brown', 'charlie@example.com', '2022-06-20', 'UK');

-- Products
INSERT INTO Products (product_name, category, price, stock_quantity) VALUES
('Laptop', 'Electronics', 800.00, 50),
('Headphones', 'Electronics', 50.00, 200),
('Coffee Maker', 'Appliances', 100.00, 30);

-- Orders
INSERT INTO Orders (customer_id, order_date, total_amount) VALUES
(1, '2023-01-10', 850.00),
(2, '2023-01-15', 150.00),
(3, '2023-01-20', 100.00);

-- Order Details
INSERT INTO Order_Details (order_id, product_id, quantity, total_price) VALUES
(1, 1, 1, 800.00),
(1, 2, 1, 50.00),
(2, 2, 3, 150.00),
(3, 3, 1, 100.00);

-- Suppliers
INSERT INTO Suppliers (supplier_name, contact) VALUES
('Tech Supplies Inc.', 'techsupplies@example.com'),
('Appliance Co.', 'applianceco@example.com');

-- Product Suppliers
INSERT INTO Product_Suppliers (product_id, supplier_id) VALUES
(1, 1),
(2, 1),
(3, 2);

-- Step 3: Analysis Queries
-- a. Sales Trend Analysis
SELECT 
    DATE_FORMAT(order_date, '%Y-%m') AS month, 
    SUM(total_amount) AS total_sales
FROM Orders
GROUP BY month
ORDER BY month;

-- b. Top-Selling Products
SELECT 
    P.product_name, 
    SUM(OD.quantity) AS total_sold
FROM Order_Details OD
JOIN Products P ON OD.product_id = P.product_id
GROUP BY P.product_name
ORDER BY total_sold DESC
LIMIT 10;

-- c. Customer Segmentation
SELECT 
    country, 
    COUNT(customer_id) AS total_customers, 
    SUM(total_amount) AS total_revenue
FROM Customers C
JOIN Orders O ON C.customer_id = O.customer_id
GROUP BY country
ORDER BY total_revenue DESC;

-- d. Inventory Status
SELECT 
    product_name, 
    stock_quantity
FROM Products
WHERE stock_quantity < 10
ORDER BY stock_quantity ASC;
