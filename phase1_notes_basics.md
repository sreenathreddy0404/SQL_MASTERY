# SQL MASTERY — PHASE 1 NOTES
## SQL & DATABASE FOUNDATIONS

---

# 1. Data

### Definition
Data is a collection of raw facts or values that can be stored, processed, and used to generate information.

### Example

```text
1
Rahul Sharma
Hyderabad
25
```

These are individual pieces of data.

---

# 2. Information

### Definition
Information is meaningful data that has been organized or processed so that it provides useful context.

### Example

```text
Customer ID: 1
Name: Rahul Sharma
City: Hyderabad
Age: 25
```

---

# 3. Database

### Definition
A database is an organized collection of data that allows data to be stored, retrieved, updated, and managed efficiently.

### Example

Our practice database:

```text
sql_mastery
│
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

---

# 4. DBMS

### Full Form
**Database Management System**

### Definition
A DBMS is software used to create, store, retrieve, update, delete, and manage data in databases.

### Examples

```text
MySQL
Oracle
PostgreSQL
SQL Server
```

---

# 5. RDBMS

### Full Form
**Relational Database Management System**

### Definition
An RDBMS is a database management system that stores data primarily in tables and establishes relationships between those tables.

### Example

```text
customers
    │
    │ customer_id
    ↓
orders
```

MySQL is an RDBMS.

---

# 6. SQL

### Full Form
**Structured Query Language**

### Definition
SQL is a language used to communicate with relational databases to create, retrieve, modify, and manage data.

### Example

```sql
SELECT *
FROM customers;
```

This retrieves all records from the `customers` table.

---

# 7. MySQL

### Definition
MySQL is a relational database management system (RDBMS) that uses SQL to store and manage relational data.

### Example

We can use MySQL to execute:

```sql
SELECT *
FROM customers;
```

### Remember

```text
SQL   → Language
MySQL → RDBMS software
```

---

# 8. SQL vs MySQL

| SQL | MySQL |
|---|---|
| Language | RDBMS software |
| Used to write queries | Executes SQL queries |
| Standard query language | Database management system |
| Example: `SELECT` | Example: MySQL Server |

### Simple Example

```text
SQL
 ↓
SELECT * FROM customers;

MySQL
 ↓
Executes the SQL query
```

---

# 9. Table

### Definition
A table is a collection of related data organized into rows and columns.

### Example

```text
customers

customer_id | name          | city
------------|---------------|----------
1           | Rahul Sharma  | Hyderabad
2           | Priya Reddy   | Bangalore
3           | Arjun Kumar   | Chennai
```

---

# 10. Row

### Definition
A row represents one complete record in a table.

### Example

```text
1 | Rahul Sharma | Hyderabad
```

This represents one customer.

### Remember

```text
1 Row = 1 Record
```

---

# 11. Column

### Definition
A column represents a specific attribute or property of the data stored in a table.

### Example

```text
customer_id
name
city
age
```

Each one is a column.

### Remember

```text
Column = Attribute / Property
```

---

# 12. Schema

### Definition
A schema is the structure or design of a database. It defines tables, columns, data types, constraints, keys, and relationships.

### Example

```text
customers
│
├── customer_id INT
├── name VARCHAR(100)
├── email VARCHAR(100)
├── city VARCHAR(50)
├── signup_date DATE
└── age INT
```

### Remember

```text
Schema = Structure of Database
```

---

# 13. Entity

### Definition
An entity is a real-world object or concept about which we want to store information.

### Examples

```text
Customer
Employee
Product
Order
Payment
Department
```

---

# 14. Attribute

### Definition
An attribute is a property or characteristic of an entity.

### Example

For the `Customer` entity:

```text
Customer
│
├── customer_id
├── name
├── email
├── city
└── age
```

Here, `name`, `email`, `city`, and `age` are attributes.

### Remember

```text
Entity    = Thing
Attribute = Property of the thing
```

---

# 15. Primary Key

### Definition
A primary key is a column or set of columns that uniquely identifies each row in a table.

### Properties

- Must be unique
- Cannot contain NULL
- Identifies each record

### Example

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100)
);
```

Here:

```text
customer_id → Primary Key
```

---

# 16. Foreign Key

### Definition
A foreign key is a column that references a key in another table and is used to establish a relationship between tables.

### Example

```text
customers
-----------
customer_id
     ↑
     │
orders
-----------
customer_id
```

In our database:

```text
orders.customer_id
        ↓
customers.customer_id
```

### Remember

```text
Primary Key → Identifies a record

Foreign Key → Connects tables
```

---

# 17. Composite Key

### Definition
A composite key is a key made up of two or more columns that together uniquely identify a record.

### Example

```text
student_id + course_id
```

```text
student_id | course_id
-----------|----------
1          | 101
1          | 102
2          | 101
```

The combination uniquely identifies each enrollment.

---

# 18. Relationship

### Definition
A relationship describes how entities/tables are connected to each other.

### Common Types

```text
One-to-One
One-to-Many
Many-to-Many
```

### Example

One customer can have many orders:

```text
Customer
   │
   ├── Order 1
   ├── Order 2
   └── Order 3
```

Therefore:

```text
customers → orders
One-to-Many
```

---

# 19. One-to-One Relationship

### Definition
One record in one table is associated with one record in another table.

### Example

```text
Person
  │
  ↓
Passport
```

One person has one passport.

---

# 20. One-to-Many Relationship

### Definition
One record in one table can be associated with many records in another table.

### Example

```text
Customer
   │
   ├── Order 1
   ├── Order 2
   └── Order 3
```

Our database:

```text
customers → orders
```

---

# 21. Many-to-Many Relationship

### Definition
Many records in one table can be associated with many records in another table.

### Example

```text
Students ←→ Courses
```

A student can take many courses, and a course can have many students.

Usually, a junction/intermediate table is used.

```text
students
    ↓
enrollments
    ↓
courses
```

---

# 22. DDL

### Full Form
**Data Definition Language**

### Definition
DDL commands are used to create or modify the structure of database objects.

### Main Commands

```text
CREATE
ALTER
DROP
TRUNCATE
```

### Example

```sql
CREATE TABLE students (
    id INT,
    name VARCHAR(100)
);
```

---

# 23. DML

### Full Form
**Data Manipulation Language**

### Definition
DML commands are used to insert, modify, and delete data inside tables.

### Main Commands

```text
INSERT
UPDATE
DELETE
```

### Example

```sql
INSERT INTO students
VALUES (1, 'Rahul');
```

---

# 24. DQL

### Full Form
**Data Query Language**

### Definition
DQL is used to retrieve data from a database.

### Main Command

```text
SELECT
```

### Example

```sql
SELECT *
FROM customers;
```

---

# 25. DCL

### Full Form
**Data Control Language**

### Definition
DCL commands are used to control access and permissions on database objects.

### Main Commands

```text
GRANT
REVOKE
```

### Example

```sql
GRANT SELECT
ON sql_mastery.*
TO some_user;
```

---

# 26. TCL

### Full Form
**Transaction Control Language**

### Definition
TCL commands are used to manage database transactions.

### Main Commands

```text
COMMIT
ROLLBACK
SAVEPOINT
```

### Example

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 1000
WHERE account_id = 1;

ROLLBACK;
```

---

# 27. CRUD

### Definition
CRUD represents the four basic operations performed on data.

```text
C → Create
R → Read
U → Update
D → Delete
```

### SQL Mapping

```text
Create → INSERT
Read   → SELECT
Update → UPDATE
Delete → DELETE
```

---

# 28. INT

### Definition
`INT` is a numeric data type used to store whole numbers.

### Example

```sql
age INT
```

Values:

```text
20
25
30
```

---

# 29. BIGINT

### Definition
`BIGINT` is an integer data type used for very large whole numbers.

### Example

```sql
user_id BIGINT
```

Useful when identifiers may become very large.

---

# 30. DECIMAL

### Definition
`DECIMAL` stores exact decimal numbers and is commonly used for financial values.

### Example

```sql
salary DECIMAL(10,2)
```

Example value:

```text
75000.50
```

### Remember

```text
Money → DECIMAL
```

---

# 31. CHAR

### Definition
`CHAR` stores fixed-length strings.

### Example

```sql
country_code CHAR(2)
```

Values:

```text
IN
US
UK
```

---

# 32. VARCHAR

### Definition
`VARCHAR` stores variable-length strings.

### Example

```sql
name VARCHAR(100)
```

Values can have different lengths:

```text
Rahul
Rahul Sharma
Sreenath Reddy
```

---

# 33. TEXT

### Definition
`TEXT` is used to store larger amounts of text.

### Example

```sql
description TEXT
```

---

# 34. DATE

### Definition
`DATE` stores a calendar date.

### Format

```text
YYYY-MM-DD
```

### Example

```sql
signup_date DATE
```

Value:

```text
2024-01-15
```

---

# 35. DATETIME

### Definition
`DATETIME` stores both date and time.

### Format

```text
YYYY-MM-DD HH:MM:SS
```

### Example

```text
2024-01-15 14:30:00
```

---

# 36. TIMESTAMP

### Definition
`TIMESTAMP` stores date and time and has MySQL-specific behavior for timestamp handling, including time-zone-related conversion.

### Example

```sql
created_at TIMESTAMP
```

---

# 37. BOOLEAN

### Definition
`BOOLEAN` represents a true/false value.

### Example

```sql
is_active BOOLEAN
```

In MySQL, `BOOLEAN` is effectively represented using `TINYINT(1)`.

---

# 38. ENUM

### Definition
`ENUM` allows a column to contain one value from a predefined list.

### Example

```sql
status ENUM('Pending', 'Completed', 'Cancelled')
```

---

# 39. JSON

### Definition
`JSON` stores JSON-formatted structured data.

### Example

```sql
metadata JSON
```

Example value:

```json
{
    "source": "mobile",
    "version": 2
}
```

---

# 40. SHOW DATABASES

### Definition
Displays all databases available on the MySQL server.

### Command

```sql
SHOW DATABASES;
```

---

# 41. CREATE DATABASE

### Definition
Creates a new database.

### Command

```sql
CREATE DATABASE sql_mastery;
```

Safer version:

```sql
CREATE DATABASE IF NOT EXISTS sql_mastery;
```

---

# 42. USE

### Definition
Selects the database that will be used for subsequent SQL commands.

### Command

```sql
USE sql_mastery;
```

---

# 43. SELECT DATABASE()

### Definition
Returns the name of the currently selected database.

### Command

```sql
SELECT DATABASE();
```

### Example Output

```text
sql_mastery
```

---

# 44. SHOW TABLES

### Definition
Displays all tables in the currently selected database.

### Command

```sql
SHOW TABLES;
```

---

# 45. DESCRIBE / DESC

### Definition
Displays the structure of a table, including columns, data types, NULL information, keys, and defaults.

### Command

```sql
DESC customers;
```

or:

```sql
DESCRIBE customers;
```

---

# 46. SHOW CREATE TABLE

### Definition
Displays the SQL statement used to create a table, including its structure and constraints.

### Command

```sql
SHOW CREATE TABLE customers;
```

---

# 47. CREATE TABLE

### Definition
Creates a new table with specified columns, data types, and constraints.

### Example

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100),
    age INT
);
```

---

# 48. ALTER TABLE

### Definition
Modifies the structure of an existing table.

### Add a column

```sql
ALTER TABLE students
ADD email VARCHAR(100);
```

### Modify a column

```sql
ALTER TABLE students
MODIFY name VARCHAR(150);
```

### Rename a column

```sql
ALTER TABLE students
RENAME COLUMN name TO full_name;
```

### Drop a column

```sql
ALTER TABLE students
DROP COLUMN age;
```

---

# 49. DROP TABLE

### Definition
Deletes the table and its data from the database.

### Command

```sql
DROP TABLE students;
```

### Remember

```text
DROP
↓
Table + Data removed
```

---

# 50. TRUNCATE TABLE

### Definition
Removes all rows from a table while keeping the table structure.

### Command

```sql
TRUNCATE TABLE students;
```

### Remember

```text
TRUNCATE
↓
Rows removed
Structure remains
```

---

# 51. DELETE

### Definition
Removes rows from a table and can be used with a `WHERE` condition.

### Example

```sql
DELETE FROM customers
WHERE customer_id = 5;
```

Only the matching row is targeted.

---

# 52. DELETE vs TRUNCATE vs DROP

| Command | Removes Rows | Removes Table | Can Filter |
|---|---:|---:|---:|
| DELETE | ✅ | ❌ | ✅ |
| TRUNCATE | ✅ All | ❌ | ❌ |
| DROP | ✅ | ✅ | ❌ |

### Easy Memory Trick

```text
DELETE
→ Delete selected data

TRUNCATE
→ Empty the table

DROP
→ Destroy the table
```

---

# 53. Referential Integrity

### Definition
Referential integrity ensures that relationships between related tables remain valid.

### Example

If:

```text
orders.customer_id = 5
```

then customer `5` should exist in:

```text
customers.customer_id
```

This is enforced using a foreign key.

---

# 54. Foreign Key Relationship in Our Database

Our practice database contains:

```text
customers
    │
    │ customer_id
    ↓
orders
```

```text
orders
    │
    │ order_id
    ↓
order_items
```

```text
products
    │
    │ product_id
    ↓
order_items
```

```text
categories
    │
    │ category_id
    ↓
products
```

These relationships are defined in the practice database schema. 

---

# 55. Self-Referencing Relationship

### Definition
A self-referencing relationship occurs when a table has a foreign-key-like relationship to another row in the same table.

### Our Example

The `employees` table contains:

```text
employee_id
manager_id
```

Here:

```text
manager_id → another employee's employee_id
```

Example:

```text
Amit Sharma
employee_id = 1

Ravi Kumar
employee_id = 2
manager_id = 1
```

Therefore:

```text
Ravi Kumar
    ↓
Manager
    ↓
Amit Sharma
```

This will become important when we learn **SELF JOIN**.

---

# 56. Our SQL Mastery Database

We will use this database throughout the course:

```text
sql_mastery
│
├── customers
│
├── orders
│
├── order_items
│
├── products
│
├── categories
│
├── payments
│
├── reviews
│
├── employees
│
└── departments
```

---

# 57. Important Relationships

```text
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
```

And:

```text
departments
      ↑
      │ department_id
      │
  employees
      │
      │ manager_id
      ↓
  employees
```

---

# ⭐ PHASE 1 — ONE-PAGE REVISION

## Database Fundamentals

```text
Data
→ Raw facts

Information
→ Processed/organized meaningful data

Database
→ Organized collection of data

DBMS
→ Software that manages databases

RDBMS
→ DBMS based on relational tables

SQL
→ Language used to interact with relational databases

MySQL
→ RDBMS that uses SQL
```

## Relational Concepts

```text
Table
→ Rows + Columns

Row
→ Record

Column
→ Attribute

Schema
→ Database structure

Entity
→ Real-world object

Attribute
→ Property of an entity
```

## Keys

```text
Primary Key
→ Uniquely identifies a row

Foreign Key
→ Connects related tables

Composite Key
→ Multiple columns forming a key
```

## SQL Categories

```text
DDL
→ CREATE
→ ALTER
→ DROP
→ TRUNCATE

DML
→ INSERT
→ UPDATE
→ DELETE

DQL
→ SELECT

DCL
→ GRANT
→ REVOKE

TCL
→ COMMIT
→ ROLLBACK
→ SAVEPOINT
```

## Important Data Types

```text
INT
→ Whole numbers

BIGINT
→ Large whole numbers

DECIMAL
→ Exact decimal values

CHAR
→ Fixed-length strings

VARCHAR
→ Variable-length strings

TEXT
→ Large text

DATE
→ Date

DATETIME
→ Date + Time

TIMESTAMP
→ Date + Time with MySQL timestamp behavior

BOOLEAN
→ True/False

ENUM
→ Predefined values

JSON
→ JSON data
```

## Important MySQL Commands

```sql
SHOW DATABASES;

CREATE DATABASE sql_mastery;

USE sql_mastery;

SELECT DATABASE();

SHOW TABLES;

DESC customers;

SHOW CREATE TABLE customers;
```

---

# 🎯 PHASE 1 GOLDEN RULES

```text
1. SQL ≠ MySQL

2. MySQL is an RDBMS.

3. A table contains rows and columns.

4. A row represents one record.

5. A column represents an attribute.

6. Primary Key identifies a row.

7. Foreign Key establishes a relationship.

8. DDL changes structure.

9. DML changes data.

10. DQL retrieves data.

11. DCL controls permissions.

12. TCL controls transactions.

13. DECIMAL is preferred for exact monetary values.

14. DELETE removes rows.
    TRUNCATE empties the table.
    DROP removes the table.

15. Schema describes the database structure.
```

# 🏆 PHASE 1 COMPLETE

Your next phase is:

**PHASE 2 — BASIC SQL QUERIES**

We'll start with the most important SQL command:

```sql
SELECT
```

and gradually build:

```text
SELECT
  ↓
FROM
  ↓
WHERE
  ↓
ORDER BY
  ↓
LIMIT
  ↓
DISTINCT
```

All exercises will use your existing `sql_mastery` database and its actual tables/data.