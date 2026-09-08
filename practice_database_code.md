For the course, I recommend using **one realistic company database** instead of creating a new tiny table for every topic.

We can use the same database throughout all phases—from `SELECT` and `WHERE` all the way to **joins, subqueries, CTEs, window functions, dates, transactions, indexes, and optimization**.

# 🗄️ Our Practice Database

Let's call it:

```sql
sql_mastery
```

It will contain:

```text
customers
    ↓
orders
    ↓
order_items
    ↓
products
    ↓
categories

employees
    ↓
departments

payments
reviews
```

This gives us enough relationships to create hundreds of interview-style problems.

---

# 1. Create Database

Run this first:

```sql
CREATE DATABASE sql_mastery;
USE sql_mastery;
```

---

# 2. Customers

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    city VARCHAR(50),
    state VARCHAR(50),
    country VARCHAR(50),
    signup_date DATE,
    age INT
);
```

### Insert data

```sql
INSERT INTO customers VALUES
(1,'Rahul Sharma','rahul@gmail.com','Hyderabad','Telangana','India','2023-01-15',25),
(2,'Priya Reddy','priya@gmail.com','Bangalore','Karnataka','India','2023-02-20',28),
(3,'Arjun Kumar','arjun@gmail.com','Chennai','Tamil Nadu','India','2023-03-10',24),
(4,'Sneha Patel','sneha@gmail.com','Ahmedabad','Gujarat','India','2023-04-05',27),
(5,'Vamsi Krishna','vamsi@gmail.com','Vijayawada','Andhra Pradesh','India','2023-05-18',26),
(6,'Ananya Singh','ananya@gmail.com','Mumbai','Maharashtra','India','2023-06-12',30),
(7,'Rohit Verma','rohit@gmail.com','Delhi','Delhi','India','2023-07-01',29),
(8,'Kavya Rao','kavya@gmail.com','Pune','Maharashtra','India','2023-08-14',23),
(9,'Aditya Mehta','aditya@gmail.com','Surat','Gujarat','India','2023-09-22',31),
(10,'Neha Gupta','neha@gmail.com','Jaipur','Rajasthan','India','2023-10-10',26),
(11,'Kiran Reddy','kiran@gmail.com','Kadapa','Andhra Pradesh','India','2023-11-05',32),
(12,'Meghana Das','meghana@gmail.com','Kolkata','West Bengal','India','2023-12-15',25),
(13,'Sahil Khan','sahil@gmail.com','Delhi','Delhi','India','2024-01-10',28),
(14,'Pooja Shah','pooja@gmail.com','Mumbai','Maharashtra','India','2024-02-18',24),
(15,'Nikhil Jain','nikhil@gmail.com','Bangalore','Karnataka','India','2024-03-25',30);
```

---

# 3. Departments

This table is especially useful for **self joins and employee hierarchy problems**.

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50),
    location VARCHAR(50)
);
```

```sql
INSERT INTO departments VALUES
(1,'Engineering','Bangalore'),
(2,'HR','Hyderabad'),
(3,'Sales','Mumbai'),
(4,'Marketing','Delhi'),
(5,'Finance','Bangalore'),
(6,'Operations','Chennai');
```

---

# 4. Employees

This is one of our most important tables.

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    department_id INT,
    manager_id INT NULL,
    salary DECIMAL(10,2),
    job_title VARCHAR(100),
    hire_date DATE,
    city VARCHAR(50),
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

```sql
INSERT INTO employees VALUES
(1,'Amit Sharma',1,NULL,120000,'Engineering Manager','2020-01-10','Bangalore'),
(2,'Ravi Kumar',1,1,95000,'Senior Developer','2021-03-15','Bangalore'),
(3,'Suresh Reddy',1,1,85000,'Developer','2022-06-20','Hyderabad'),
(4,'Neeraj Singh',1,2,70000,'Developer','2023-02-10','Bangalore'),
(5,'Karthik Rao',1,2,68000,'Developer','2023-08-05','Chennai'),
(6,'Pooja Mehta',2,NULL,90000,'HR Manager','2020-04-12','Hyderabad'),
(7,'Divya Patel',2,6,60000,'HR Executive','2022-01-18','Ahmedabad'),
(8,'Manoj Verma',3,NULL,100000,'Sales Manager','2019-11-01','Mumbai'),
(9,'Akash Gupta',3,8,75000,'Sales Executive','2021-07-22','Delhi'),
(10,'Ramesh Das',3,8,72000,'Sales Executive','2022-09-15','Kolkata'),
(11,'Swati Jain',4,NULL,88000,'Marketing Manager','2020-08-20','Delhi'),
(12,'Anil Kumar',4,11,62000,'Marketing Executive','2023-01-12','Delhi'),
(13,'Deepak Shah',5,NULL,110000,'Finance Manager','2019-05-10','Bangalore'),
(14,'Meena Rao',5,13,70000,'Financial Analyst','2021-10-25','Bangalore'),
(15,'Ganesh Kumar',6,NULL,85000,'Operations Manager','2020-12-01','Chennai');
```

Notice something important:

```text
employee.manager_id → employee.employee_id
```

So we can practice **self joins**.

---

# 5. Categories

```sql
CREATE TABLE categories (
    category_id INT PRIMARY KEY,
    category_name VARCHAR(50)
);
```

```sql
INSERT INTO categories VALUES
(1,'Electronics'),
(2,'Clothing'),
(3,'Books'),
(4,'Home & Kitchen'),
(5,'Sports');
```

---

# 6. Products

This table gives us plenty of opportunities for aggregation and joins.

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    category_id INT,
    price DECIMAL(10,2),
    stock INT,
    rating DECIMAL(3,2),
    created_date DATE,
    FOREIGN KEY (category_id) REFERENCES categories(category_id)
);
```

```sql
INSERT INTO products VALUES
(1,'Laptop',1,75000,20,4.50,'2023-01-10'),
(2,'Smartphone',1,35000,50,4.30,'2023-02-15'),
(3,'Headphones',1,2500,100,4.20,'2023-03-20'),
(4,'Keyboard',1,1800,80,4.10,'2023-04-12'),
(5,'T-Shirt',2,800,150,4.00,'2023-05-01'),
(6,'Jeans',2,1800,90,4.20,'2023-05-15'),
(7,'Jacket',2,3500,60,4.40,'2023-06-10'),
(8,'SQL Book',3,900,100,4.70,'2023-07-05'),
(9,'Python Book',3,850,120,4.60,'2023-07-20'),
(10,'Data Structures Book',3,1100,80,4.80,'2023-08-15'),
(11,'Mixer Grinder',4,4500,40,4.10,'2023-09-10'),
(12,'Coffee Maker',4,5500,35,4.30,'2023-09-25'),
(13,'Cricket Bat',5,3000,50,4.50,'2023-10-10'),
(14,'Football',5,1200,100,4.20,'2023-10-20'),
(15,'Running Shoes',5,4000,70,4.60,'2023-11-05');
```

---

# 7. Orders 

This table will be extremely important.

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    status VARCHAR(30),
    total_amount DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

```sql
INSERT INTO orders VALUES
(101,1,'2024-01-05','Completed',75000),
(102,2,'2024-01-10','Completed',35000),
(103,3,'2024-01-15','Cancelled',2500),
(104,4,'2024-01-20','Completed',5300),
(105,5,'2024-02-05','Completed',4400),
(106,6,'2024-02-10','Completed',9000),
(107,7,'2024-02-15','Pending',1800),
(108,8,'2024-02-20','Completed',8500),
(109,9,'2024-03-01','Completed',11000),
(110,10,'2024-03-05','Completed',4000),
(111,1,'2024-03-10','Completed',5300),
(112,2,'2024-03-15','Cancelled',1800),
(113,3,'2024-03-20','Completed',850),
(114,4,'2024-04-01','Completed',75000),
(115,5,'2024-04-10','Pending',3000),
(116,6,'2024-04-15','Completed',5500),
(117,7,'2024-05-01','Completed',1200),
(118,8,'2024-05-10','Completed',4000),
(119,9,'2024-05-15','Completed',35000),
(120,10,'2024-06-01','Completed',2500),
(121,11,'2024-06-05','Completed',900),
(122,12,'2024-06-10','Completed',1100),
(123,13,'2024-07-01','Completed',75000),
(124,14,'2024-07-05','Cancelled',4500),
(125,15,'2024-07-10','Completed',1800);
```

---

# 8. Order Items 

This is crucial because real-world SQL rarely operates on only one table.

```sql
CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    unit_price DECIMAL(10,2),
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

```sql
INSERT INTO order_items VALUES
(1,101,1,1,75000),
(2,102,2,1,35000),
(3,103,3,1,2500),
(4,104,5,2,800),
(5,104,6,2,1800),
(6,105,6,1,1800),
(7,105,5,2,800),
(8,106,11,2,4500),
(9,107,4,1,1800),
(10,108,9,10,850),
(11,109,10,10,1100),
(12,110,15,1,4000),
(13,111,5,2,800),
(14,111,3,1,2500),
(15,112,4,1,1800),
(16,113,9,1,850),
(17,114,1,1,75000),
(18,115,13,1,3000),
(19,116,12,1,5500),
(20,117,14,1,1200),
(21,118,15,1,4000),
(22,119,2,1,35000),
(23,120,3,1,2500),
(24,121,8,1,900),
(25,122,10,1,1100),
(26,123,1,1,75000),
(27,124,11,1,4500),
(28,125,6,1,1800);
```

---

# 9. Payments

Useful for **joins, NULLs, aggregation and real-world scenarios**.

```sql
CREATE TABLE payments (
    payment_id INT PRIMARY KEY,
    order_id INT,
    payment_date DATE,
    amount DECIMAL(10,2),
    payment_method VARCHAR(30),
    payment_status VARCHAR(30),
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
);
```

```sql
INSERT INTO payments VALUES
(1,101,'2024-01-05',75000,'Credit Card','Success'),
(2,102,'2024-01-10',35000,'UPI','Success'),
(3,104,'2024-01-20',5300,'Debit Card','Success'),
(4,105,'2024-02-05',4400,'UPI','Success'),
(5,106,'2024-02-10',9000,'Credit Card','Success'),
(6,108,'2024-02-20',8500,'UPI','Success'),
(7,109,'2024-03-01',11000,'Net Banking','Success'),
(8,110,'2024-03-05',4000,'UPI','Success'),
(9,111,'2024-03-10',5300,'Credit Card','Success'),
(10,113,'2024-03-20',850,'UPI','Success'),
(11,114,'2024-04-01',75000,'Credit Card','Success'),
(12,116,'2024-04-15',5500,'UPI','Success'),
(13,117,'2024-05-01',1200,'Cash','Success'),
(14,118,'2024-05-10',4000,'UPI','Success'),
(15,119,'2024-05-15',35000,'Credit Card','Success'),
(16,120,'2024-06-01',2500,'UPI','Success'),
(17,121,'2024-06-05',900,'UPI','Success'),
(18,122,'2024-06-10',1100,'Debit Card','Success'),
(19,123,'2024-07-01',75000,'Credit Card','Success'),
(20,125,'2024-07-10',1800,'UPI','Success');
```

---

# 10. Reviews

Useful for **aggregation and ranking**.

```sql
CREATE TABLE reviews (
    review_id INT PRIMARY KEY,
    customer_id INT,
    product_id INT,
    rating INT,
    review_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

```sql
INSERT INTO reviews VALUES
(1,1,1,5,'2024-01-15'),
(2,2,2,4,'2024-01-20'),
(3,3,3,4,'2024-01-25'),
(4,4,5,5,'2024-02-01'),
(5,5,6,4,'2024-02-10'),
(6,6,11,4,'2024-02-15'),
(7,8,9,5,'2024-02-25'),
(8,9,10,5,'2024-03-05'),
(9,10,15,4,'2024-03-10'),
(10,1,5,4,'2024-03-15'),
(11,2,4,3,'2024-03-20'),
(12,3,9,5,'2024-03-25'),
(13,4,1,5,'2024-04-10'),
(14,6,12,4,'2024-04-20'),
(15,8,15,5,'2024-05-15');
```

---

# 🔗 Understand the Relationships

Keep this mental model:

```text
                    departments
                        │
                        │
                   employees
                        │
                    manager_id
                        │
                   employees


customers
    │
    │ customer_id
    ↓
 orders
    │
    │ order_id
    ↓
order_items
    │
    │ product_id
    ↓
products
    │
    │ category_id
    ↓
categories


orders
   │
   ↓
payments


customers ────── reviews ────── products
```

This schema is deliberately designed so we can eventually write queries involving **4–6 tables**.

---

#  What We Can Practice With This Database

### Phase 1

```text
SELECT
WHERE
ORDER BY
LIMIT
DISTINCT
LIKE
BETWEEN
IN
NULL
```

Example:

> Find all customers from Maharashtra.

---

### Phase 2

```text
GROUP BY
HAVING
COUNT
SUM
AVG
MIN
MAX
```

Example:

> Find the average salary of employees in each department.

---

### Phase 3

```text
INNER JOIN
LEFT JOIN
RIGHT JOIN
SELF JOIN
```

Example:

> Find every employee and their manager's name.

---

### Phase 4

```text
Subqueries
Correlated subqueries
EXISTS
NOT EXISTS
IN
```

Example:

> Find employees earning more than the average company salary.

---

### Phase 5

```text
CTE
Multiple CTEs
Recursive CTE
```

Example:

> Build an employee-management hierarchy.

---

### Phase 6 🔥

```text
ROW_NUMBER()
RANK()
DENSE_RANK()
LAG()
LEAD()
SUM() OVER()
AVG() OVER()
PARTITION BY
```

Example:

> Find the top 3 highest-paid employees in every department.

---

### Phase 7 🔥🔥

Business problems:

> Find each customer's first order.

> Find each customer's latest order.

> Find customers who never placed an order.

> Find the most purchased product.

> Find the second-highest selling product.

> Find monthly revenue.

> Find month-over-month revenue growth.

> Find customers whose spending is above the average customer spending.

> Find the highest-paid employee in every department.

> Find employees earning more than their manager.

> Find products that have never been ordered.

> Find customers who bought product A but never bought product B.

---

# ⚠️ Important

The dataset above is intentionally **small** so that you can understand and manually trace queries.

Later, when we reach **indexes, EXPLAIN, optimization and performance**, I'll give you a **large-data generator** that creates:

```text
100,000+ customers
500,000+ orders
1,000,000+ order_items
100,000+ products
```

Then you'll actually see why an index can change a query from slow to fast.

---

# 🎯 How We'll Use This During Our Course

We will **freeze this schema as our SQL Mastery database**.

From now on, when we learn a concept, we'll use these tables wherever possible.

For example:

**Learning `GROUP BY`**

→ We'll solve problems using `customers`, `orders`, `employees`, etc.

**Learning JOINs**

→ We'll combine:

```text
customers → orders → order_items → products
```

**Learning window functions**

→ We'll solve ranking, running-total, previous-order and top-N problems.

**Learning optimization**

→ We'll run `EXPLAIN` on these same queries and improve them.

This way, you're not learning isolated SQL syntax. You're gradually becoming comfortable with **one realistic relational database and increasingly complex queries**.
