# PHASE 2 — CRUD & FILTERING

## 1. CRUD
CRUD means:
- **C**reate → `INSERT`
- **R**ead → `SELECT`
- **U**pdate → `UPDATE`
- **D**elete → `DELETE`

---

## 2. INSERT

### Insert one row
```sql
INSERT INTO customers
VALUES (16,'Varun Kumar','varun@gmail.com','Hyderabad','Telangana','India','2024-04-01',27);
```

### Insert using specific columns
```sql
INSERT INTO customers (customer_id,name,email,city,age)
VALUES (17,'Ravi','ravi@gmail.com','Chennai',25);
```

### Insert multiple rows
```sql
INSERT INTO customers (customer_id,name,email,city,age)
VALUES
(18,'A','a@gmail.com','Delhi',24),
(19,'B','b@gmail.com','Mumbai',26);
```

**Best practice:** Specify column names instead of depending on table column order.

---

## 3. SELECT

```sql
SELECT name, email
FROM customers;
```

All columns:
```sql
SELECT *
FROM customers;
```

`SELECT *` is useful while learning/debugging, but avoid it unnecessarily in production queries.

---

## 4. DISTINCT

Removes duplicate values.

```sql
SELECT DISTINCT city
FROM customers;
```

Multiple columns:
```sql
SELECT DISTINCT city, state
FROM customers;
```

---

## 5. WHERE

Filters rows.

```sql
SELECT *
FROM customers
WHERE age > 25;
```

Comparison operators:

```text
=     equal
!=    not equal
<>    not equal
>     greater than
<     less than
>=    greater than or equal
<=    less than or equal
```

---

## 6. AND / OR / NOT

### AND
Both conditions must be true.

```sql
SELECT *
FROM customers
WHERE age > 25 AND city = 'Mumbai';
```

### OR
At least one condition must be true.

```sql
SELECT *
FROM customers
WHERE city = 'Mumbai' OR city = 'Delhi';
```

### NOT
Negates a condition.

```sql
SELECT *
FROM customers
WHERE NOT city = 'Mumbai';
```

### Use parentheses with mixed conditions

```sql
SELECT *
FROM customers
WHERE state = 'Gujarat'
AND (age > 25 OR city = 'Surat');
```

---

## 7. IN / NOT IN

Instead of multiple `OR` conditions:

```sql
SELECT *
FROM customers
WHERE city IN ('Mumbai','Delhi','Pune');
```

```sql
SELECT *
FROM customers
WHERE city NOT IN ('Mumbai','Delhi');
```

---

## 8. BETWEEN

Checks whether a value is within a range.

```sql
SELECT *
FROM customers
WHERE age BETWEEN 25 AND 30;
```

`BETWEEN` is **inclusive**.

Equivalent:
```sql
WHERE age >= 25 AND age <= 30
```

---

## 9. LIKE

Used for pattern matching.

### Starts with
```sql
SELECT *
FROM customers
WHERE name LIKE 'Rah%';
```

### Ends with
```sql
WHERE name LIKE '%Reddy';
```

### Contains
```sql
WHERE name LIKE '%reddy%';
```

### Exactly one character
`_` represents one character.

```sql
WHERE name LIKE 'A____';
```

`%` → zero or more characters  
`_` → exactly one character

---

## 10. NULL

`NULL` means **missing/unknown value**.

Wrong:
```sql
WHERE email = NULL
```

Correct:
```sql
WHERE email IS NULL;
```

```sql
WHERE email IS NOT NULL;
```

Important:
```text
NULL is not 0
NULL is not ''
NULL is not FALSE
```

---

## 11. ORDER BY

Sorts the result.

Ascending:
```sql
SELECT *
FROM products
ORDER BY price ASC;
```

Descending:
```sql
SELECT *
FROM products
ORDER BY price DESC;
```

Multiple columns:
```sql
SELECT *
FROM products
ORDER BY category_id ASC, price DESC;
```

First sorts by `category_id`; within each category, sorts by price descending.

---

## 12. LIMIT

Restricts number of rows.

```sql
SELECT *
FROM products
ORDER BY price DESC
LIMIT 5;
```

Returns the 5 most expensive products.

---

## 13. OFFSET

Skips rows.

```sql
SELECT *
FROM products
ORDER BY product_id
LIMIT 5 OFFSET 5;
```

Skips the first 5 rows and returns the next 5.

Basic pagination:

```text
Page 1 → LIMIT 5 OFFSET 0
Page 2 → LIMIT 5 OFFSET 5
Page 3 → LIMIT 5 OFFSET 10
```

---

## 14. UPDATE

Changes existing data.

```sql
UPDATE products
SET price = 80000
WHERE product_id = 1;
```

Multiple columns:

```sql
UPDATE products
SET price = 78000,
    stock = 25
WHERE product_id = 1;
```

Using the existing value:

```sql
UPDATE products
SET price = price * 1.10
WHERE category_id = 1;
```

**Important:** Always be careful with `UPDATE` without `WHERE`.

```sql
UPDATE products
SET price = 1000;
```

This updates **every row**.

---

## 15. DELETE

Deletes rows.

```sql
DELETE FROM customers
WHERE customer_id = 15;
```

Without `WHERE`:

```sql
DELETE FROM customers;
```

This deletes **all rows**.

---

## 16. DELETE vs TRUNCATE vs DROP

| Command | Removes rows | Removes table |
|---|---|---|
| DELETE | Yes | No |
| TRUNCATE | All rows | No |
| DROP | All rows | Yes |

```sql
DELETE FROM customers WHERE customer_id = 1;

TRUNCATE TABLE customers;

DROP TABLE customers;
```

---

## 17. SQL COMMENTS

Single-line:
```sql
-- Get expensive products
SELECT *
FROM products
WHERE price > 50000;
```

Multi-line:
```sql
/*
Get all
customers
*/
SELECT *
FROM customers;
```

---

## 18. ALIAS

Temporary name for a column:

```sql
SELECT name AS customer_name
FROM customers;
```

Table alias:

```sql
SELECT c.name
FROM customers AS c;
```

`AS` is optional for table aliases:

```sql
FROM customers c
```

Aliases become especially important when we learn **JOINs**.

---

## 19. LOGICAL ORDER OF SQL

Although we normally write:

```sql
SELECT
FROM
WHERE
ORDER BY
LIMIT
```

SQL logically processes approximately as:

```text
FROM
↓
WHERE
↓
GROUP BY
↓
HAVING
↓
SELECT
↓
DISTINCT
↓
ORDER BY
↓
LIMIT
```

This becomes very important for understanding complex queries.

---

## 20. BASIC QUERY-BUILDING PATTERN

```sql
SELECT columns
FROM table
WHERE condition
ORDER BY column
LIMIT number;
```

Example:

```sql
SELECT product_name, price
FROM products
WHERE price > 2000
ORDER BY price DESC
LIMIT 5;
```

Think:

```text
Which columns?  → SELECT
Which table?    → FROM
Which rows?     → WHERE
How to sort?    → ORDER BY
How many?       → LIMIT
```

## Phase 2 Core Skills

By the end of Phase 2, you should be comfortable with:

```text
INSERT
SELECT
DISTINCT
WHERE
AND / OR / NOT
IN / NOT IN
BETWEEN
LIKE
IS NULL / IS NOT NULL
ORDER BY
LIMIT / OFFSET
UPDATE
DELETE
Aliases
Logical query order
```

# 🧪 Phase 2 Practical Practice

Using our sql_mastery database, try these without looking for the solution first.
--- 
## Level 1
1. Display all customers.
2. Display only customer names and cities.
3. Display all employees with salary greater than 80000.
4. Find employees from Bangalore.
5. Find employees whose salary is between 70000 and 100000.
6. Find customers from Maharashtra or Gujarat.
7. Find employees belonging to departments 1, 3, or 5.
8. Find employees whose names start with A.
9. Find employees whose names contain a.
10. Find employees who don't have a manager.

--- 

## Level 2
11. Find the 5 highest-paid employees.
12. Find the 3 lowest-paid employees.
13. Find employees from Bangalore earning more than 70000.
14. Find employees from Bangalore or Hyderabad earning more than 80000.
15. Find customers who signed up after 2023-06-01.
16. Find products costing between 1000 and 5000.
17. Find products with rating greater than 4.5.
18. Display employees ordered by salary descending.
19. Display employees ordered by department and then salary descending.
20. Display the second page of employees, with 5 employees per page.

--- 

## Level 3 🔥
21. Find the highest-paid employee.
22. Find the lowest-paid employee.
23. Find the 3 highest-paid employees in Engineering.
24. Find employees whose names start with R and salary is greater than 70000.
25. Find products that are either in categories 1, 3, or 5 and have a rating above 4.3.
26. Find customers from Maharashtra who are older than 25.
27. Increase the salary of all Engineering employees by 10%.
28. Change the city of employee 4 to Hyderabad.
29. Delete a test customer that you inserted.
30. Insert 3 new customers in a single query.