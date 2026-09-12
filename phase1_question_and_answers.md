# MySQL Phase 1 — Questions & Answers
#
# Level 1 — Fundamentals
#


### 1. What is the difference between Data and Information?

<details>
<summary>Show answer</summary>

- **Data** → Raw facts that have no meaningful context by themselves.
- **Information** → Processed and organized data that has meaning.

**Example:**

```text
Data:
101, Ravi, 50000

Information:
Employee Ravi has a salary of ₹50,000.
```



---

</details>

### 2. What is a Database? Give one real-world example.

<details>
<summary>Show answer</summary>

A **database** is an organized collection of data that can be stored, accessed, managed, and modified efficiently.

**Real-world example:**

An e-commerce company's database may store:

```text
Customers
Orders
Products
Payments
Reviews
```



---

</details>

### 3. What is a DBMS? Name two DBMSs.

<details>
<summary>Show answer</summary>

**DBMS** stands for **Database Management System**.

It is software used to create, store, retrieve, update, and manage data in databases.

**Examples:**

- MySQL
- PostgreSQL
- Oracle Database
- Microsoft SQL Server



---

</details>

### 4. What is an RDBMS?

<details>
<summary>Show answer</summary>

**RDBMS** stands for **Relational Database Management System**.

It stores data in **tables** and establishes relationships between those tables using keys.

**Example:**

```text
customers
    ↓
orders
    ↓
order_items
    ↓
products
```

MySQL is an RDBMS.



---

</details>

### 5. What is the difference between SQL and MySQL?

<details>
<summary>Show answer</summary>

| SQL | MySQL |
|---|---|
| SQL = Structured Query Language | MySQL = Database Management System |
| It is a language | It is software |
| Used to communicate with databases | Uses SQL to manage databases |
| Standardized language | Specific database product |

**Simple way to remember:**

> **SQL is the language; MySQL is the software that understands and executes SQL.**



---

</details>

### 6. What is a Table?

<details>
<summary>Show answer</summary>

A **table** is a structure in a relational database used to store data in **rows and columns**.

Example:

```text
customers

customer_id | name       | email
------------|------------|----------------
1           | Ravi Kumar | ravi@gmail.com
2           | Anil       | anil@gmail.com
```



---

</details>

### 7. What is the difference between a Row and a Column?

<details>
<summary>Show answer</summary>

- **Row** → Represents one complete record.
- **Column** → Represents one attribute/property of the records.

Example:

```text
customer_id | name       | email
------------|------------|----------------
1           | Ravi Kumar | ravi@gmail.com
```

Here:

- Row → `1, Ravi Kumar, ravi@gmail.com`
- Columns → `customer_id`, `name`, `email`



---

</details>

### 8. What is a Schema?

<details>
<summary>Show answer</summary>

A **schema** describes the structure/design of a database.

It defines things such as:

- Tables
- Columns
- Data types
- Primary keys
- Foreign keys
- Relationships
- Constraints

**Example:**

```text
sql_mastery
    ├── customers
    ├── orders
    ├── order_items
    ├── products
    ├── categories
    ├── employees
    ├── departments
    ├── payments
    └── reviews
```

This overall structure is part of the database schema.



---

</details>

### 9. What is an Entity and what is an Attribute?

<details>
<summary>Show answer</summary>

**Entity** → A real-world object about which we store information.

**Attribute** → A property/characteristic of an entity.

Example:

```text
Entity: Customer

Attributes:
- customer_id
- name
- email
- phone
```

So:

> **Customer = Entity**

> **name, email, phone = Attributes**



---

</details>

### 10. In the `customers` table, identify Entity, Attributes and Primary Key.

<details>
<summary>Show answer</summary>

**Entity:**

```text
Customer
```

**Attributes:**

The columns of the `customers` table represent its attributes.

**Primary Key:**

The column that uniquely identifies each customer is the **primary key** of `customers`.

To verify the exact column name in your database:

```sql
DESC customers;
```

or:

```sql
SHOW CREATE TABLE customers;
```

---
#
# Level 2 — Keys & Relationships
#
</details>

## 11. What is a Primary Key? List its important properties.

<details>
<summary>Show answer</summary>

A **Primary Key (PK)** is a column or combination of columns that uniquely identifies every row in a table.

### Important properties:

1. Must uniquely identify a row.
2. Cannot contain `NULL`.
3. A table can have only one primary key constraint.
4. It can consist of one or multiple columns.
5. It should identify records reliably.

Example:

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100)
);
```

Here:

```text
student_id → Primary Key
```



---

</details>

## 12. Why can't a Primary Key contain NULL?

<details>
<summary>Show answer</summary>

A primary key must uniquely identify every row.

`NULL` means **unknown/missing value**.

If a primary key could contain `NULL`, we couldn't reliably identify that row.

Therefore:

```text
PRIMARY KEY = UNIQUE + NOT NULL
```



---

</details>

## 13. What is a Foreign Key?

<details>
<summary>Show answer</summary>

A **Foreign Key (FK)** is a column that refers to the primary key of another table.

It creates a relationship between tables.

Example:

```text
customers
-----------
customer_id ← PK

orders
-----------
order_id
customer_id ← FK
```

`orders.customer_id` refers to `customers.customer_id`.



---

</details>

## 14. In the `sql_mastery` database, which column connects `customers` and `orders`?

<details>
<summary>Show answer</summary>

The relationship is through:

```text
customers.customer_id
        ↑
        |
orders.customer_id
```

So:

```text
orders.customer_id → customers.customer_id
```

`orders.customer_id` is the foreign key.



---

</details>

## 15. Which column connects `orders` and `order_items`?

<details>
<summary>Show answer</summary>

They are connected through:

```text
orders.order_id
      ↑
      |
order_items.order_id
```

So:

```text
order_items.order_id → orders.order_id
```



---

</details>

## 16. Which columns connect `order_items` and `products`?

<details>
<summary>Show answer</summary>

They are connected through:

```text
order_items.product_id
        ↓
products.product_id
```

So:

```text
order_items.product_id → products.product_id
```



---

</details>

## 17. What type of relationship exists between customers → orders, orders → order_items, products → categories?

<details>
<summary>Show answer</summary>

### Customers → Orders

**One-to-Many (1:N)**

One customer can place many orders.

```text
Customer
   |
   ├── Order 1
   ├── Order 2
   └── Order 3
```

### Orders → Order Items

**One-to-Many (1:N)**

One order can contain multiple order items.

```text
Order 101
   |
   ├── Item 1
   ├── Item 2
   └── Item 3
```

### Products → Categories

Typically **Many-to-One (N:1)** if each product belongs to one category.

```text
Product 1 ─┐
Product 2 ─┼──→ Category
Product 3 ─┘
```

Therefore, from the category perspective:

```text
Category → Products = One-to-Many
```



---

</details>

## 18. What is a Composite Key? Give a simple example.

<details>
<summary>Show answer</summary>

A **Composite Key** is a primary key made using **two or more columns together**.

Example:

```text
order_items
----------------
order_id
product_id
quantity
```

Suppose:

```text
order_id | product_id
---------|-----------
101      | 5
101      | 8
102      | 5
```

The combination:

```text
(order_id, product_id)
```

can uniquely identify an item within an order.

Example:

```sql
PRIMARY KEY (order_id, product_id)
```



---

</details>

## 19. What is a Self-Referencing Relationship?

<details>
<summary>Show answer</summary>

A **self-referencing relationship** occurs when a table has a foreign key that refers back to the same table.

Example:

```text
employees
----------------
employee_id
name
manager_id
```

`manager_id` refers to another `employee_id` in the same table.

Example:

```text
Ravi
 ↓
Manager: Priya
```

Both Ravi and Priya are stored in `employees`.



---

</details>

## 20. In the `employees` table, which column creates the self-referencing relationship?

<details>
<summary>Show answer</summary>

```text
manager_id
```

It references:

```text
employees.employee_id
```

So:

```text
employees.manager_id
        ↓
employees.employee_id
```

This allows an employee to have another employee as their manager.

---


</details>

#
#  Level 3 — SQL Command Categories
#

## 21. What does DDL stand for? Name four DDL commands.

<details>
<summary>Show answer</summary>

**DDL = Data Definition Language**

DDL is used to define or modify database structures.

Common commands:

```text
CREATE
ALTER
DROP
TRUNCATE
```

Example:

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY
);
```



---

</details>

## 22. What does DML stand for? Name three DML commands.

<details>
<summary>Show answer</summary>

**DML = Data Manipulation Language**

Used to manipulate data inside tables.

Common commands:

```text
INSERT
UPDATE
DELETE
```

Example:

```sql
INSERT INTO students
VALUES (1, 'Ravi', 21);
```



---

</details>

## 23. What does DQL stand for? Which command belongs to it?

<details>
<summary>Show answer</summary>

**DQL = Data Query Language**

It is used to retrieve data.

Main command:

```sql
SELECT
```

Example:

```sql
SELECT * FROM customers;
```



---

</details>

## 24. What is DCL? Name two commands.

<details>
<summary>Show answer</summary>

**DCL = Data Control Language**

It controls user permissions and access to database objects.

Commands:

```text
GRANT
REVOKE
```

Example:

```sql
GRANT SELECT ON sql_mastery.* TO 'user'@'localhost';
```



---

</details>

## 25. What is TCL? Name three commands.

<details>
<summary>Show answer</summary>

**TCL = Transaction Control Language**

It manages transactions.

Common commands:

```text
COMMIT
ROLLBACK
SAVEPOINT
```

Example:

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 100
WHERE id = 1;

COMMIT;
```



---

</details>

## 26. Match the commands with their categories.

<details>
<summary>Show answer</summary>

| Command | Category |
|---|---|
| CREATE | DDL |
| INSERT | DML |
| SELECT | DQL |
| GRANT | DCL |
| COMMIT | TCL |
| DELETE | DML |
| ALTER | DDL |



---

</details>

## 27. What does CRUD stand for?

<details>
<summary>Show answer</summary>

CRUD stands for:

```text
C → Create
R → Read
U → Update
D → Delete
```

These are the four basic operations performed on data.



---

</details>

## 28. Map CRUD operations to SQL commands.

<details>
<summary>Show answer</summary>

| CRUD | SQL |
|---|---|
| Create | INSERT |
| Read | SELECT |
| Update | UPDATE |
| Delete | DELETE |

Example:

```sql
-- Create
INSERT INTO customers (...) VALUES (...);

-- Read
SELECT * FROM customers;

-- Update
UPDATE customers SET name = 'Ravi' WHERE customer_id = 1;

-- Delete
DELETE FROM customers WHERE customer_id = 1;
```


</details>
---

#  Level 4 — MySQL Commands

## 29. Write the MySQL command to display all databases.

<details>
<summary>Show answer</summary>

```sql
SHOW DATABASES;
```



---

</details>

## 30. Write the command to select the `sql_mastery` database.

<details>
<summary>Show answer</summary>

```sql
USE sql_mastery;
```



---

</details>

## 31. Write the command to check which database you are currently using.

<details>
<summary>Show answer</summary>

```sql
SELECT DATABASE();
```



---

</details>

## 32. Write the command to display all tables inside the current database.

<details>
<summary>Show answer</summary>

```sql
SHOW TABLES;
```



---

</details>

## 33. Write the command to see the structure of the `customers` table.

<details>
<summary>Show answer</summary>

```sql
DESC customers;
```

or:

```sql
DESCRIBE customers;
```



---

</details>

## 34. Write the command to see the complete CREATE TABLE definition of `customers`.

<details>
<summary>Show answer</summary>

```sql
SHOW CREATE TABLE customers;
```

This is particularly useful for seeing:

- Primary key
- Foreign keys
- Data types
- Constraints
- Indexes
- Table engine



---

</details>

## 35. Write a command to create a database named `practice_db`.

<details>
<summary>Show answer</summary>

```sql
CREATE DATABASE practice_db;
```

A safer version:

```sql
CREATE DATABASE IF NOT EXISTS practice_db;
```



---

</details>

## 36. Write a command to create the `students` table and make `student_id` the primary key.

<details>
<summary>Show answer</summary>

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100),
    age INT
);
```



---

</details>

## 37. Write a command to add an `email` column to the `students` table.

<details>
<summary>Show answer</summary>

```sql
ALTER TABLE students
ADD COLUMN email VARCHAR(255);
```



---

</details>

## 38. Write a command to remove the `students` table completely.

<details>
<summary>Show answer</summary>

```sql
DROP TABLE students;
```

⚠️ This removes the table structure and its data.


</details>
---

#  Level 5 — Data Types

## 39. Which data type would you choose?

<details>
<summary>Show answer</summary>

| Requirement | Suitable Data Type |
|---|---|
| Customer ID | `INT` |
| Product price | `DECIMAL(10,2)` |
| Customer name | `VARCHAR(100)` |
| Date of birth | `DATE` |
| Large numeric ID | `BIGINT` |
| Short fixed-length code | `CHAR(n)` |
| Long article/content | `TEXT` |



---

</details>

## 40. Why should DECIMAL generally be preferred over FLOAT for money?

<details>
<summary>Show answer</summary>

`FLOAT` stores numbers using floating-point representation, which can introduce small rounding errors.

Money requires accurate decimal calculations.

Therefore:

```sql
DECIMAL(10,2)
```

is generally preferred.

Example:

```text
DECIMAL:
100.25

FLOAT:
May have tiny representation/rounding differences internally.
```

**Rule:**

> 💰 Money → Prefer `DECIMAL`.



---

</details>

## 41. What is the difference between CHAR and VARCHAR?

<details>
<summary>Show answer</summary>

### CHAR

Fixed-length string.

```sql
CHAR(5)
```

If the value is:

```text
"ABC"
```

it uses fixed-length storage behavior.

### VARCHAR

Variable-length string.

```sql
VARCHAR(100)
```

It stores strings according to their actual length, subject to the maximum.

**Simple rule:**

```text
CHAR    → Fixed length
VARCHAR → Variable length
```

Example:

```text
Country code → CHAR(2)
Name         → VARCHAR(100)
```



---

</details>

## 42. What is the difference between DATE, DATETIME and TIMESTAMP?

<details>
<summary>Show answer</summary>

### DATE

Stores only the date.

```text
2026-09-08
```

### DATETIME

Stores date and time.

```text
2026-09-08 18:30:00
```

### TIMESTAMP

Stores date and time and is commonly used for timestamp/audit fields. It also has MySQL-specific automatic initialization/update capabilities and timezone-related behavior.

**Simple rule:**

```text
DATE      → Date only
DATETIME  → Date + Time
TIMESTAMP → Timestamp/date-time value
```



---

</details>

## 43. What is the purpose of the BOOLEAN data type?

<details>
<summary>Show answer</summary>

`BOOLEAN` is used for values representing **true/false** states.

Example:

```sql
is_active BOOLEAN
```

Conceptually:

```text
TRUE
FALSE
```

In MySQL, `BOOLEAN` is effectively an alias for `TINYINT(1)`.



---

</details>

## 44. When might you use JSON in MySQL?

<details>
<summary>Show answer</summary>

Use `JSON` when you need to store semi-structured data whose fields may vary.

Example:

```json
{
    "theme": "dark",
    "notifications": true,
    "language": "en"
}
```

However, JSON should not automatically replace properly designed relational columns.

**Rule:**

> Stable, frequently queried data → normal columns.

> Flexible/semi-structured data → JSON can be useful.


</details>
---

#  Level 6 — Important Differences

## 45. Explain DELETE vs TRUNCATE vs DROP.

<details>
<summary>Show answer</summary>

| Command | Removes Rows | Removes Table | WHERE allowed |
|---|---:|---:|---:|
| DELETE | ✅ | ❌ | ✅ |
| TRUNCATE | ✅ All | ❌ | ❌ |
| DROP | ✅ | ✅ | ❌ |

### DELETE

Removes rows.

```sql
DELETE FROM customers
WHERE customer_id = 5;
```

The table remains.

### TRUNCATE

Removes all rows.

```sql
TRUNCATE TABLE customers;
```

The table structure remains.

### DROP

Removes the entire table.

```sql
DROP TABLE customers;
```

Both the structure and data are removed.



---

</details>

## 46. Which command removes rows but keeps the table structure?

<details>
<summary>Show answer</summary>

```sql
DELETE
```

or:

```sql
TRUNCATE
```

depending on whether you want to remove selected rows or all rows.



---

</details>

## 47. Which command removes the entire table structure?

<details>
<summary>Show answer</summary>

```sql
DROP TABLE
```



---

</details>

## 48. Which command can remove specific rows based on a condition?

<details>
<summary>Show answer</summary>

```sql
DELETE
```

Example:

```sql
DELETE FROM customers
WHERE customer_id = 10;
```



---

</details>

## 49. What happens with `TRUNCATE TABLE customers;`?

<details>
<summary>Show answer</summary>

```sql
TRUNCATE TABLE customers;
```

It removes **all rows** from `customers`.

But:

```text
Table structure → remains
Table → still exists
Rows → removed
```

You cannot use:

```sql
TRUNCATE TABLE customers
WHERE customer_id = 10;
```

because `TRUNCATE` does not support a `WHERE` condition.



---

</details>

## 50. What happens with `DROP TABLE customers;`?

<details>
<summary>Show answer</summary>

```sql
DROP TABLE customers;
```

The entire table is removed.

```text
Table → ❌
Rows → ❌
Structure → ❌
```

Afterward:

```sql
SELECT * FROM customers;
```

will fail because the table no longer exists.


</details>
---

#  Level 7 — Database Thinking

## 51. Identify the primary key of each table.

<details>
<summary>Show answer</summary>

For your `sql_mastery` database, the exact PK column names should be verified from the schema using:

```sql
SHOW CREATE TABLE customers;
SHOW CREATE TABLE orders;
SHOW CREATE TABLE products;
SHOW CREATE TABLE categories;
SHOW CREATE TABLE employees;
SHOW CREATE TABLE departments;
SHOW CREATE TABLE payments;
SHOW CREATE TABLE reviews;
```

The important idea is:

```text
customers    → customer identifier
orders       → order identifier
products     → product identifier
categories   → category identifier
employees    → employee identifier
departments  → department identifier
payments     → payment identifier
reviews      → review identifier
```

Use `SHOW CREATE TABLE` when you need the **exact constraint definition**, rather than guessing from column names.



---

</details>

## 52. Identify every foreign-key relationship you can find.

<details>
<summary>Show answer</summary>

The key relationships in the schema described are:

```text
customers
    ↑
    |
orders.customer_id
```

```text
orders
    ↑
    |
order_items.order_id
```

```text
products
    ↑
    |
order_items.product_id
```

```text
categories
    ↑
    |
products.category_id
```

```text
employees
    ↑
    |
employees.manager_id
```

There can also be relationships involving:

```text
payments
reviews
departments
```

depending on the exact foreign-key definitions in your uploaded schema.

To identify **every FK exactly**, run:

```sql
SHOW CREATE TABLE table_name;
```

for each table.



---

</details>

## 53. "Which customer placed order 101?" Which tables would you need?

<details>
<summary>Show answer</summary>

You need:

```text
orders
customers
```

Relationship:

```text
customers.customer_id
          ↑
          |
orders.customer_id
```

The `orders` table tells us which customer ID placed order `101`, and `customers` gives us that customer's details.



---

</details>

## 54. "Which products are present in order 101?" Which tables would you need?

<details>
<summary>Show answer</summary>

You need:

```text
orders
order_items
products
```

Relationship:

```text
orders
  ↓
order_items
  ↓
products
```

Why?

- `orders` identifies order `101`.
- `order_items` identifies which products belong to that order.
- `products` contains product details.



---

</details>

## 55. "Which department does employee Ravi Kumar belong to?" Which tables would you need?

<details>
<summary>Show answer</summary>

You need:

```text
employees
departments
```

Relationship:

```text
employees.department_id
          ↓
departments.department_id
```

The `employees` table identifies Ravi Kumar's department, while `departments` provides department information.



---

</details>

## 56. Why does `order_items` need both `order_id` and `product_id`?

<details>
<summary>Show answer</summary>

Because `order_items` represents the relationship between an **order** and the **products inside that order**.

Example:

```text
Order 101
    |
    ├── Product 5
    ├── Product 8
    └── Product 12
```

So:

```text
order_id  → Which order?
product_id → Which product?
```

It may also contain:

```text
quantity
price
```

to describe that particular item in the order.



---

</details>

## 57. Why shouldn't we store all product information directly inside the `orders` table?

<details>
<summary>Show answer</summary>

Because it would create **data duplication** and make the database difficult to maintain.

Bad design:

```text
orders

order_id | product_name | product_price | category | ...
```

An order can contain multiple products, so we'd either need repeated columns or duplicate rows.

Better design:

```text
orders
   ↓
order_items
   ↓
products
```

This follows relational database design principles and reduces unnecessary duplication.



---

</details>

## 58. Why is the `employees.manager_id` column useful?

<details>
<summary>Show answer</summary>

It allows us to represent the **organizational hierarchy**.

Example:

```text
CEO
 |
 ├── Manager A
 │    ├── Employee 1
 │    └── Employee 2
 |
 └── Manager B
      └── Employee 3
```

`manager_id` tells us which employee manages another employee.

It creates a **self-referencing relationship**.



---

</details>

## 59. What would happen if `orders.customer_id` contained a customer ID that doesn't exist in `customers`?

<details>
<summary>Show answer</summary>

If the foreign-key constraint is properly defined, MySQL will reject the insert/update.

Example:

```text
customers

customer_id
-----------
1
2
3
```

Trying:

```text
orders.customer_id = 999
```

would fail because customer `999` does not exist.

This is called **referential integrity**.



---

</details>

## 60. Explain the overall relationship.

<details>
<summary>Show answer</summary>

```text
Customer
   ↓
Orders
   ↓
Order Items
   ↓
Products
   ↓
Categories
```

A **customer** can place multiple **orders**.

Each **order** can contain multiple **order items**.

Each **order item** represents a particular **product** in that order.

Each **product** belongs to a **category**.

So the flow is:

```text
Customer
   |
   | places
   ↓
Order
   |
   | contains
   ↓
Order Item
   |
   | refers to
   ↓
Product
   |
   | belongs to
   ↓
Category
```

This separates different types of information into different tables instead of storing everything together.

---
</details>

# 🏆 Final Challenge — Phase 1

## 61. You join a company and are given an unfamiliar MySQL database. What steps would you take to understand its structure?

<details>
<summary>Show answer</summary>

I would follow a systematic process:

### Step 1 — See all databases

```sql
SHOW DATABASES;
```

### Step 2 — Select the database

```sql
USE sql_mastery;
```

### Step 3 — Check the current database

```sql
SELECT DATABASE();
```

### Step 4 — List all tables

```sql
SHOW TABLES;
```

### Step 5 — Inspect table structures

```sql
DESC customers;
DESC orders;
DESC products;
```

### Step 6 — Inspect complete definitions

```sql
SHOW CREATE TABLE customers;
SHOW CREATE TABLE orders;
```

### Step 7 — Identify

```text
Primary Keys
Foreign Keys
Data Types
Constraints
Indexes
```

### Step 8 — Understand relationships

Draw something like:

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
```

### Step 9 — Look at sample data

```sql
SELECT * FROM customers LIMIT 5;
SELECT * FROM orders LIMIT 5;
```

### Step 10 — Build a mental ER diagram

This helps understand how the entire database works.



---

</details>

## 62. Explain Primary Key vs Foreign Key with an example from `sql_mastery`.

<details>
<summary>Show answer</summary>

### Primary Key

Uniquely identifies a row in its own table.

Example:

```text
customers
----------------
customer_id ← PK
```

### Foreign Key

References a key in another table.

Example:

```text
orders
----------------
order_id
customer_id ← FK
```

Relationship:

```text
customers.customer_id
          ↑
          |
orders.customer_id
```

### Interview answer:

> A primary key uniquely identifies a record within a table, whereas a foreign key creates a relationship by referencing a key in another table.



---

</details>

## 63. Explain DDL, DML, DQL, DCL and TCL with examples.

<details>
<summary>Show answer</summary>

### DDL — Data Definition Language

Used to define/modify database structures.

```sql
CREATE TABLE students (...);

ALTER TABLE students ADD email VARCHAR(255);

DROP TABLE students;
```

Commands:

```text
CREATE
ALTER
DROP
TRUNCATE
```

---

### DML — Data Manipulation Language

Used to modify data.

```sql
INSERT INTO students VALUES (1, 'Ravi', 21);

UPDATE students
SET age = 22
WHERE student_id = 1;

DELETE FROM students
WHERE student_id = 1;
```

Commands:

```text
INSERT
UPDATE
DELETE
```

---

### DQL — Data Query Language

Used to retrieve data.

```sql
SELECT * FROM students;
```

Main command:

```text
SELECT
```

---

### DCL — Data Control Language

Used to control permissions.

```text
GRANT
REVOKE
```

---

### TCL — Transaction Control Language

Used to control transactions.

```text
COMMIT
ROLLBACK
SAVEPOINT
```



---

</details>

## 64. Explain DELETE, TRUNCATE and DROP as an interview question.

<details>
<summary>Show answer</summary>

> **DELETE** is used to remove rows from a table and can use a `WHERE` condition. The table structure remains.
>
> **TRUNCATE** removes all rows from a table but keeps the table structure. It does not support a `WHERE` condition.
>
> **DROP** removes the entire table, including its structure and data.

### Quick comparison:

```text
DELETE
  ↓
Remove selected/all rows
Keep table

TRUNCATE
  ↓
Remove all rows
Keep table

DROP
  ↓
Remove table completely
```

A useful interview point is that `DELETE` is a row-level DML operation, while `TRUNCATE` and `DROP` are DDL operations in MySQL.



---

</details>

## 65. Draw the table relationships of the entire `sql_mastery` database on paper.

<details>
<summary>Show answer</summary>

Start with the main e-commerce flow:

```text
                 ┌──────────────┐
                 │  customers   │
                 └──────┬───────┘
                        │
                        │ customer_id
                        ↓
                 ┌──────────────┐
                 │    orders    │
                 └──────┬───────┘
                        │
                        │ order_id
                        ↓
                 ┌──────────────┐
                 │ order_items  │
                 └──────┬───────┘
                        │
                        │ product_id
                        ↓
                 ┌──────────────┐
                 │   products   │
                 └──────┬───────┘
                        │
                        │ category_id
                        ↓
                 ┌──────────────┐
                 │  categories  │
                 └──────────────┘
```

Then add the employee hierarchy:

```text
departments
     ↑
     |
employees
     |
     └──── manager_id ────→ employees
```

And then inspect where:

```text
payments
reviews
```

connect to the other tables using:

```sql
SHOW CREATE TABLE payments;
SHOW CREATE TABLE reviews;
```

### Your final paper diagram should show:

```text
customers
    │
    ↓
orders
    │
    ↓
order_items ─────→ products ─────→ categories


departments
    ↑
    │
employees
    │
    └──────────────→ employees
       manager_id


payments  ───────→ [related table]
reviews   ───────→ [related table]
```

The exact arrows for `payments` and `reviews` should come from their actual foreign-key definitions rather than assumptions.

---
</details>
