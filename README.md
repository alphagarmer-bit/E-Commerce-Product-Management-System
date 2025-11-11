# E-Commerce-Product-Management-System
CREATE DATABASE IF NOT EXISTS EcommerceDB;
USE EcommerceDB;

-- 2. Create Tables with Constraints (PK, FK, NOT NULL, UNIQUE, CHECK)

-- Customers Table
CREATE TABLE Customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY, -- PK
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL, -- UNIQUE constraint
    phone_number VARCHAR(15),
    address_line1 VARCHAR(100) NOT NULL,
    city VARCHAR(50) NOT NULL,
    zip_code VARCHAR(10)
);

-- Categories Table (For Product Catalog Management)
CREATE TABLE Categories (
    category_id INT AUTO_INCREMENT PRIMARY KEY,
    category_name VARCHAR(50) UNIQUE NOT NULL
);

-- Products Table (Product Catalog)
CREATE TABLE Products (
    product_id INT AUTO_INCREMENT PRIMARY KEY, -- PK
    product_name VARCHAR(100) NOT NULL,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    stock_quantity INT NOT NULL DEFAULT 0,
    category_id INT,
    -- CHECK constraint to ensure price is positive
    CONSTRAINT chk_ProductPrice CHECK (price > 0), 
    -- CHECK constraint to ensure stock is non-negative
    CONSTRAINT chk_StockQuantity CHECK (stock_quantity >= 0),
    FOREIGN KEY (category_id) REFERENCES Categories(category_id) -- FK to Categories
);

-- Orders Table
CREATE TABLE Orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY, -- PK
    customer_id INT NOT NULL,
    order_date DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    total_amount DECIMAL(10, 2) NOT NULL DEFAULT 0.00,
    order_status ENUM('Pending', 'Processing', 'Shipped', 'Delivered', 'Cancelled') DEFAULT 'Pending',
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id) -- FK to Customers
);

-- Order_Details Table (Junction table for the many-to-many relationship between Orders and Products)
CREATE TABLE Order_Details (
    order_detail_id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    unit_price DECIMAL(10, 2) NOT NULL,
    -- Combined UNIQUE constraint to prevent duplicates for the same product in the same order
    UNIQUE KEY uk_OrderProduct (order_id, product_id),
    -- CHECK constraint for positive quantity
    CONSTRAINT chk_OrderDetailQuantity CHECK (quantity > 0), 
    FOREIGN KEY (order_id) REFERENCES Orders(order_id), -- FK to Orders
    FOREIGN KEY (product_id) REFERENCES Products(product_id) -- FK to Products
);

-- Payments Table
CREATE TABLE Payments (
    payment_id INT AUTO_INCREMENT PRIMARY KEY, -- PK
    order_id INT UNIQUE NOT NULL, -- UNIQUE FK: one payment per order
    payment_date DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    payment_method VARCHAR(50) NOT NULL,
    amount DECIMAL(10, 2) NOT NULL,
    payment_status ENUM('Completed', 'Failed', 'Refunded') DEFAULT 'Completed',
    FOREIGN KEY (order_id) REFERENCES Orders(order_id) -- FK to Orders
); 
