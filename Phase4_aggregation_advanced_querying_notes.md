# PHASE 4 — AGGREGATION & ADVANCED QUERYING

## 1. What is Aggregation?

Aggregation means processing multiple rows and producing a summary value.

Example:

```sql
SELECT AVG(price)
FROM products;
```

Instead of returning every product, SQL returns one average price.

### Main Aggregate Functions

```text
COUNT() → counts rows/values
SUM()   → calculates total
AVG()   → calculates average
MIN()   → finds minimum
MAX()   → finds maximum
```

---

# 2. COUNT()

`COUNT()` is used to count rows or non-NULL values.

## COUNT(*)

Counts all rows.

```sql
SELECT COUNT(*)
FROM customers;
```

`COUNT(*)` counts every row, even if some columns contain NULL.

## COUNT(column)

Counts only non-NULL values in that column.

```sql
SELECT COUNT(email)
FROM customers;
```

### Difference

```text
COUNT(*)       → counts rows
COUNT(column)  → counts non-NULL values
```

---

# 3. COUNT(DISTINCT)

Used to count unique values.

```sql
SELECT COUNT(DISTINCT customer_id)
FROM orders;
```

Meaning:

> How many unique customers have placed orders?

### Remember

```text
COUNT(*)              → number of rows
COUNT(column)         → number of non-NULL values
COUNT(DISTINCT col)   → number of unique non-NULL values
```

---

# 4. SUM()

`SUM()` calculates the total of a numeric column.

```sql
SELECT SUM(total_amount)
FROM orders;
```

Find total value of completed orders:

```sql
SELECT SUM(total_amount)
FROM orders
WHERE status = 'Completed';
```

---

# 5. AVG()

`AVG()` calculates the average of a numeric column.

```sql
SELECT AVG(salary)
FROM employees;
```

Average salary of Engineering employees:

```sql
SELECT AVG(salary)
FROM employees
WHERE department_id = 1;
```

---

# 6. MIN()

`MIN()` returns the smallest value.

```sql
SELECT MIN(price)
FROM products;
```

Find the lowest employee salary:

```sql
SELECT MIN(salary)
FROM employees;
```

---

# 7. MAX()

`MAX()` returns the largest value.

```sql
SELECT MAX(price)
FROM products;
```

Find the highest employee salary:

```sql
SELECT MAX(salary)
FROM employees;
```

---

# 8. Using Multiple Aggregate Functions

Multiple aggregate functions can be used together.

```sql
SELECT
    COUNT(*) AS employee_count,
    SUM(salary) AS total_salary,
    AVG(salary) AS average_salary,
    MIN(salary) AS minimum_salary,
    MAX(salary) AS maximum_salary
FROM employees;
```

This produces a summary of the entire employee table.

---

# 9. GROUP BY

`GROUP BY` divides rows into groups based on one or more columns.

Example:

```sql
SELECT
    department_id,
    AVG(salary) AS average_salary
FROM employees
GROUP BY department_id;
```

This calculates the average salary separately for every department.

### Mental Model

```text
All employees
      ↓
GROUP BY department_id
      ↓
Department 1 → employees → AVG()
Department 2 → employees → AVG()
Department 3 → employees → AVG()
...
```

---

# 10. GROUP BY + COUNT()

Find the number of employees in each department:

```sql
SELECT
    department_id,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department_id;
```

---

# 11. GROUP BY + SUM()

Find total salary paid by each department:

```sql
SELECT
    department_id,
    SUM(salary) AS total_salary
FROM employees
GROUP BY department_id;
```

---

# 12. GROUP BY + AVG()

Find average salary of each department:

```sql
SELECT
    department_id,
    AVG(salary) AS average_salary
FROM employees
GROUP BY department_id;
```

---

# 13. GROUP BY + MIN() and MAX()

Find the minimum and maximum salary in every department:

```sql
SELECT
    department_id,
    MIN(salary) AS lowest_salary,
    MAX(salary) AS highest_salary
FROM employees
GROUP BY department_id;
```

---

# 14. GROUP BY Multiple Columns

We can group by more than one column.

Example:

```sql
SELECT
    state,
    city,
    COUNT(*) AS customer_count
FROM customers
GROUP BY state, city;
```

SQL groups rows according to the combination of:

```text
state + city
```

For example:

```text
Gujarat + Ahmedabad
Gujarat + Surat
Maharashtra + Mumbai
Maharashtra + Pune
```

---

# 15. GROUP BY with JOIN

Find the number of orders placed by each customer:

```sql
SELECT
    c.customer_id,
    c.name,
    COUNT(o.order_id) AS order_count
FROM customers c
LEFT JOIN orders o
    ON c.customer_id = o.customer_id
GROUP BY
    c.customer_id,
    c.name;
```

`LEFT JOIN` is used because we also want customers who have zero orders.

---

# 16. COUNT(*) vs COUNT(column) with LEFT JOIN

Consider:

```sql
FROM customers c
LEFT JOIN orders o
    ON c.customer_id = o.customer_id
```

If a customer has no order, the result may look like:

```text
customer_id | name   | order_id
------------+--------+---------
15          | Nikhil | NULL
```

Now:

```sql
COUNT(*)
```

counts the generated row.

But:

```sql
COUNT(o.order_id)
```

does not count the NULL value.

Therefore:

```sql
COUNT(o.order_id)
```

is usually preferred when counting matching child rows after a `LEFT JOIN`.

---

# 17. GROUP BY with Multiple Tables

Find the number of products in each category:

```sql
SELECT
    c.category_name,
    COUNT(p.product_id) AS product_count
FROM categories c
LEFT JOIN products p
    ON c.category_id = p.category_id
GROUP BY
    c.category_id,
    c.category_name;
```

---

# 18. Aggregation Across Multiple Tables

Find the total quantity sold for each product:

```sql
SELECT
    p.product_name,
    SUM(oi.quantity) AS total_quantity_sold
FROM products p
JOIN order_items oi
    ON p.product_id = oi.product_id
GROUP BY
    p.product_id,
    p.product_name;
```

The pattern is:

```text
JOIN
  ↓
GROUP BY
  ↓
AGGREGATE
```

---

# 19. Aggregation Across Three Tables

Find the total quantity sold for each category:

```sql
SELECT
    c.category_name,
    SUM(oi.quantity) AS total_quantity_sold
FROM categories c
JOIN products p
    ON c.category_id = p.category_id
JOIN order_items oi
    ON p.product_id = oi.product_id
GROUP BY
    c.category_id,
    c.category_name;
```

Relationship:

```text
categories
     ↓
products
     ↓
order_items
```

---

# 20. WHERE with GROUP BY

`WHERE` filters individual rows before grouping.

Example:

```sql
SELECT
    department_id,
    AVG(salary) AS average_salary
FROM employees
WHERE salary > 70000
GROUP BY department_id;
```

Execution conceptually:

```text
FROM
 ↓
WHERE
 ↓
GROUP BY
 ↓
AVG()
```

Only employees earning more than 70,000 participate in the grouping.

---

# 21. HAVING

`HAVING` is used to filter groups after aggregation.

Example:

> Find departments whose average salary is greater than 80,000.

```sql
SELECT
    department_id,
    AVG(salary) AS average_salary
FROM employees
GROUP BY department_id
HAVING AVG(salary) > 80000;
```

---

# 22. WHERE vs HAVING

This is one of the most important Phase 4 concepts.

```text
WHERE
→ filters individual rows

HAVING
→ filters groups
```

### WHERE Example

```sql
SELECT *
FROM employees
WHERE salary > 70000;
```

This filters employees.

### HAVING Example

```sql
SELECT
    department_id,
    AVG(salary) AS average_salary
FROM employees
GROUP BY department_id
HAVING AVG(salary) > 70000;
```

This filters departments/groups.

---

# 23. WHERE + HAVING Together

Both can be used in the same query.

Example:

> Find departments whose average salary is greater than 75,000, considering only employees earning at least 70,000.

```sql
SELECT
    department_id,
    AVG(salary) AS average_salary
FROM employees
WHERE salary >= 70000
GROUP BY department_id
HAVING AVG(salary) > 75000;
```

Conceptually:

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
```

---

# 24. HAVING with COUNT()

Find customers who placed more than 2 orders:

```sql
SELECT
    customer_id,
    COUNT(*) AS order_count
FROM orders
GROUP BY customer_id
HAVING COUNT(*) > 2;
```

`COUNT(*) > 2` is an aggregate condition, so we use `HAVING`.

---

# 25. HAVING with SUM()

Find customers whose total spending is greater than 50,000:

```sql
SELECT
    customer_id,
    SUM(total_amount) AS total_spent
FROM orders
GROUP BY customer_id
HAVING SUM(total_amount) > 50000;
```

---

# 26. HAVING with AVG()

Find categories whose average product price is greater than 3,000:

```sql
SELECT
    category_id,
    AVG(price) AS average_price
FROM products
GROUP BY category_id
HAVING AVG(price) > 3000;
```

---

# 27. GROUP BY + ORDER BY

Find departments ordered by highest average salary:

```sql
SELECT
    department_id,
    AVG(salary) AS average_salary
FROM employees
GROUP BY department_id
ORDER BY average_salary DESC;
```

We can use the alias in `ORDER BY`.

---

# 28. GROUP BY + ORDER BY + LIMIT

Find the department with the highest average salary:

```sql
SELECT
    department_id,
    AVG(salary) AS average_salary
FROM employees
GROUP BY department_id
ORDER BY average_salary DESC
LIMIT 1;
```

Common pattern:

```text
GROUP BY
   ↓
AGGREGATE
   ↓
ORDER BY
   ↓
LIMIT
```

---

# 29. Conditional Aggregation

Conditional aggregation means using a condition inside an aggregate function.

Example:

```sql
SELECT
    COUNT(
        CASE
            WHEN status = 'Completed'
            THEN 1
        END
    ) AS completed_orders,

    COUNT(
        CASE
            WHEN status = 'Cancelled'
            THEN 1
        END
    ) AS cancelled_orders,

    COUNT(
        CASE
            WHEN status = 'Pending'
            THEN 1
        END
    ) AS pending_orders
FROM orders;
```

This allows multiple categories to be calculated in one query.

---

# 30. Conditional SUM()

Find completed order value:

```sql
SELECT
    SUM(
        CASE
            WHEN status = 'Completed'
            THEN total_amount
            ELSE 0
        END
    ) AS completed_revenue
FROM orders;
```

Multiple statuses can be calculated together:

```sql
SELECT
    SUM(
        CASE
            WHEN status = 'Completed'
            THEN total_amount
            ELSE 0
        END
    ) AS completed_revenue,

    SUM(
        CASE
            WHEN status = 'Cancelled'
            THEN total_amount
            ELSE 0
        END
    ) AS cancelled_value,

    SUM(
        CASE
            WHEN status = 'Pending'
            THEN total_amount
            ELSE 0
        END
    ) AS pending_value
FROM orders;
```

---

# 31. Conditional Aggregation with GROUP BY

For each customer, count completed and cancelled orders:

```sql
SELECT
    customer_id,

    COUNT(
        CASE
            WHEN status = 'Completed'
            THEN 1
        END
    ) AS completed_orders,

    COUNT(
        CASE
            WHEN status = 'Cancelled'
            THEN 1
        END
    ) AS cancelled_orders

FROM orders
GROUP BY customer_id;
```

---

# 32. SUM() with CASE

Another common form of conditional aggregation:

```sql
SELECT
    customer_id,

    SUM(
        CASE
            WHEN status = 'Completed'
            THEN 1
            ELSE 0
        END
    ) AS completed_orders,

    SUM(
        CASE
            WHEN status = 'Cancelled'
            THEN 1
            ELSE 0
        END
    ) AS cancelled_orders,

    SUM(
        CASE
            WHEN status = 'Pending'
            THEN 1
            ELSE 0
        END
    ) AS pending_orders

FROM orders
GROUP BY customer_id;
```

Both `COUNT(CASE...)` and `SUM(CASE...)` are useful patterns.

---

# 33. DISTINCT Inside Aggregate Functions

We can use `DISTINCT` inside aggregate functions.

Example:

```sql
SELECT COUNT(DISTINCT customer_id)
FROM orders;
```

Meaning:

> Count the number of unique customers who placed orders.

Another example:

```sql
SELECT COUNT(DISTINCT product_id)
FROM order_items;
```

Meaning:

> Count the number of different products that appear in orders.

---

# 34. NULL and Aggregate Functions

Most aggregate functions ignore NULL values.

For example, if:

```text
salary
------
10000
20000
NULL
30000
```

Then:

```sql
SELECT AVG(salary)
FROM employees;
```

calculates the average using:

```text
10000
20000
30000
```

The NULL value is ignored.

### Important

```text
COUNT(*)          → counts rows
COUNT(column)     → ignores NULL
SUM(column)       → ignores NULL
AVG(column)       → ignores NULL
MIN(column)       → ignores NULL
MAX(column)       → ignores NULL
```

---

# 35. GROUP BY and Selected Columns

Consider:

```sql
SELECT
    department_id,
    name,
    AVG(salary)
FROM employees
GROUP BY department_id;
```

This is problematic because multiple employees can belong to the same department.

Which employee's `name` should be returned?

Generally, a selected column that is not aggregated should appear in the `GROUP BY`.

Correct:

```sql
SELECT
    department_id,
    AVG(salary)
FROM employees
GROUP BY department_id;
```

Or:

```sql
SELECT
    department_id,
    job_title,
    AVG(salary)
FROM employees
GROUP BY
    department_id,
    job_title;
```

---

# 36. ONLY_FULL_GROUP_BY

Modern MySQL commonly uses the SQL mode:

```text
ONLY_FULL_GROUP_BY
```

It prevents ambiguous `GROUP BY` queries.

General rule:

```text
SELECT column
+
aggregate function
+
GROUP BY
```

Non-aggregate selected columns should generally be included in the `GROUP BY`.

---

# 37. JOIN + Aggregation Duplicate Problem

This is a very important real-world problem.

Consider:

```text
orders
   ↓
order_items
```

One order can contain multiple items.

For example:

```text
Order 104
    T-Shirt → quantity 2
    Jeans   → quantity 2
```

After joining:

```text
Order 104 → T-Shirt
Order 104 → Jeans
```

The order appears twice.

Therefore, blindly doing:

```sql
SELECT SUM(o.total_amount)
FROM orders o
JOIN order_items oi
    ON o.order_id = oi.order_id;
```

can overcount `o.total_amount`.

---

# 38. Row Multiplication / Fan-Out

Suppose:

```text
Order 1 → 3 items
Order 2 → 2 items
Order 3 → 1 item
```

After the JOIN:

```text
Order 1 → 3 rows
Order 2 → 2 rows
Order 3 → 1 row
```

Therefore, values from the `orders` table are repeated.

This is called:

```text
Row multiplication
or
Fan-out
```

Before using an aggregate after a JOIN, always ask:

> What does one row represent after this JOIN?

---

# 39. Grain of a Query

The **grain** means what one row represents.

Examples:

```text
customers
→ one row = one customer

orders
→ one row = one order

order_items
→ one row = one order item

customers JOIN orders
→ one row = one customer-order relationship

orders JOIN order_items
→ one row = one order item
```

Understanding the grain is extremely important for complex SQL.

---

# 40. Aggregation with Dates

Count orders placed on each date:

```sql
SELECT
    order_date,
    COUNT(*) AS order_count
FROM orders
GROUP BY order_date
ORDER BY order_date;
```

Total completed revenue by date:

```sql
SELECT
    order_date,
    SUM(total_amount) AS revenue
FROM orders
WHERE status = 'Completed'
GROUP BY order_date
ORDER BY order_date;
```

---

# 41. Aggregation by Month

Count orders for each month:

```sql
SELECT
    YEAR(order_date) AS year,
    MONTH(order_date) AS month,
    COUNT(*) AS order_count
FROM orders
GROUP BY
    YEAR(order_date),
    MONTH(order_date)
ORDER BY
    year,
    month;
```

---

# 42. Revenue by Month

```sql
SELECT
    YEAR(order_date) AS year,
    MONTH(order_date) AS month,
    SUM(total_amount) AS revenue
FROM orders
WHERE status = 'Completed'
GROUP BY
    YEAR(order_date),
    MONTH(order_date)
ORDER BY
    year,
    month;
```

This type of query is commonly used in reports and dashboards.

---

# 43. DISTINCT vs GROUP BY

### DISTINCT

```sql
SELECT DISTINCT state
FROM customers;
```

Purpose:

> Remove duplicate values from the result.

### GROUP BY

```sql
SELECT
    state,
    COUNT(*) AS customer_count
FROM customers
GROUP BY state;
```

Purpose:

> Create groups so that aggregate calculations can be performed.

Remember:

```text
DISTINCT → remove duplicate result values

GROUP BY → create groups for aggregation
```

---

# 44. Complete Aggregation Query Pattern

A common Phase 4 query structure is:

```sql
SELECT
    group_column,
    AGGREGATE_FUNCTION(column) AS result
FROM table
JOIN another_table
    ON condition
WHERE row_condition
GROUP BY group_column
HAVING aggregate_condition
ORDER BY result DESC
LIMIT number;
```

Example:

```sql
SELECT
    c.category_name,
    SUM(oi.quantity) AS total_units
FROM categories c
JOIN products p
    ON c.category_id = p.category_id
JOIN order_items oi
    ON p.product_id = oi.product_id
WHERE p.price > 1000
GROUP BY
    c.category_id,
    c.category_name
HAVING SUM(oi.quantity) > 5
ORDER BY total_units DESC
LIMIT 3;
```

---

# 45. Logical Query Processing Order

For Phase 4, remember this simplified order:

```text
FROM
 ↓
JOIN
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

This explains why:

```text
WHERE → row filtering

GROUP BY → grouping

HAVING → group filtering
```

---

# 46. Most Important Phase 4 Patterns

## Pattern 1 — Overall Summary

```sql
SELECT
    COUNT(*),
    SUM(column),
    AVG(column),
    MIN(column),
    MAX(column)
FROM table;
```

## Pattern 2 — Group Summary

```sql
SELECT
    group_column,
    COUNT(*),
    SUM(column),
    AVG(column)
FROM table
GROUP BY group_column;
```

## Pattern 3 — Filter Groups

```sql
SELECT
    group_column,
    COUNT(*)
FROM table
GROUP BY group_column
HAVING COUNT(*) > value;
```

## Pattern 4 — Conditional Aggregation

```sql
SELECT
    SUM(
        CASE
            WHEN condition
            THEN value
            ELSE 0
        END
    )
FROM table;
```

## Pattern 5 — Aggregation After JOIN

```sql
SELECT
    group_column,
    SUM(value)
FROM table1
JOIN table2
    ON condition
GROUP BY group_column;
```

## Pattern 6 — Top Group

```sql
SELECT
    group_column,
    SUM(value) AS total
FROM table
GROUP BY group_column
ORDER BY total DESC
LIMIT 1;
```

---

# 47. Common Phase 4 Mistakes

### Mistake 1 — Using WHERE with aggregate functions

Wrong:

```sql
WHERE COUNT(*) > 5
```

Correct:

```sql
HAVING COUNT(*) > 5
```

---

### Mistake 2 — Forgetting GROUP BY

Wrong:

```sql
SELECT department_id, AVG(salary)
FROM employees;
```

Correct:

```sql
SELECT
    department_id,
    AVG(salary)
FROM employees
GROUP BY department_id;
```

---

### Mistake 3 — Using COUNT(*) incorrectly with LEFT JOIN

When counting matching child rows:

```sql
COUNT(child_table.id)
```

is usually more appropriate than:

```sql
COUNT(*)
```

---

### Mistake 4 — Blindly using DISTINCT

Do not use `DISTINCT` simply to hide duplicates caused by a bad JOIN.

First understand why the duplicates exist.

---

### Mistake 5 — Ignoring row multiplication

A one-to-many JOIN can multiply rows and cause incorrect aggregate results.

---

### Mistake 6 — Forgetting NULL behavior

Remember:

```text
COUNT(column)
SUM(column)
AVG(column)
MIN(column)
MAX(column)
```

ignore NULL values.

---

# 48. Interview Questions

### 1. What is an aggregate function?

An aggregate function processes multiple rows and returns a summarized result.

Examples:

```text
COUNT()
SUM()
AVG()
MIN()
MAX()
```

### 2. What is GROUP BY?

`GROUP BY` divides rows into groups based on one or more columns so aggregate calculations can be performed separately for each group.

### 3. Difference between WHERE and HAVING?

```text
WHERE  → filters rows
HAVING → filters groups
```

### 4. Difference between COUNT(*) and COUNT(column)?

```text
COUNT(*)       → counts rows
COUNT(column)  → counts non-NULL values
```

### 5. Why use COUNT(column) with LEFT JOIN?

Because unmatched rows contain NULL on the right side. `COUNT(column)` therefore returns zero for groups with no matching child rows.

### 6. Can GROUP BY contain multiple columns?

Yes.

```sql
GROUP BY state, city;
```

### 7. Can WHERE contain aggregate functions?

Generally no.

Use:

```sql
HAVING
```

for aggregate-based filtering.

### 8. What is conditional aggregation?

Using `CASE` inside aggregate functions to calculate different categories in a single query.

### 9. What is row multiplication?

When a JOIN causes one original row to appear multiple times because of a one-to-many relationship.

### 10. What is query grain?

The meaning of one row in the intermediate or final result.

---

# 49. Phase 4 Practice Questions

## Level 1 — Basic Aggregation

1. Find the total number of customers.

2. Find the total number of employees.

3. Find the total number of products.

4. Find the total value of all orders.

5. Find the average employee salary.

6. Find the highest employee salary.

7. Find the lowest employee salary.

8. Find the average product price.

9. Find the most expensive product price.

10. Find the number of unique customers who placed orders.

---

## Level 2 — GROUP BY

11. Find the number of employees in each department.

12. Find the average salary of each department.

13. Find the total salary of each department.

14. Find the highest salary in each department.

15. Find the lowest salary in each department.

16. Find the number of customers in each state.

17. Find the number of customers in each city.

18. Find the number of products in each category.

19. Find the average product price in each category.

20. Find the total stock available in each category.

---

## Level 3 — HAVING

21. Find departments having more than 2 employees.

22. Find departments whose average salary is greater than 80,000.

23. Find customers who placed more than 1 order.

24. Find customers whose total order value is greater than 50,000.

25. Find categories having more than 2 products.

26. Find categories whose average product price is greater than 2,000.

27. Find customers whose total completed order value exceeds 50,000.

---

## Level 4 — Conditional Aggregation

28. Count completed, cancelled, and pending orders.

29. Find completed, cancelled, and pending order values.

30. For each customer, count completed and cancelled orders.

31. For each customer, calculate completed order value.

32. For each department, count employees earning more than 80,000.

33. For each category, count products costing more than 3,000.

---

## Level 5 — JOIN + Aggregation

34. Find the number of orders placed by each customer.

35. Find customers who have never placed an order.

36. Find total spending of each customer.

37. Find total completed spending of each customer.

38. Find total quantity sold for each product.

39. Find total quantity sold for each category.

40. Find the top 3 products by quantity sold.

41. Find the category with the highest total quantity sold.

42. Find the customer who spent the most money.

43. Find the average order value for each customer.

---

# 50. Phase 4 Final Mental Model

```text
                MULTIPLE ROWS
                     │
                     ▼
              AGGREGATE DATA
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
      COUNT         SUM          AVG
        │            │            │
        └────────────┼────────────┘
                     ▼
                  GROUP BY
                     │
                     ▼
                CREATE GROUPS
                     │
                     ▼
                   HAVING
                     │
                     ▼
                FILTER GROUPS
                     │
                     ▼
                 ORDER BY
                     │
                     ▼
                  LIMIT
```

### The three concepts you must master

```text
WHERE
→ filters rows

GROUP BY
→ creates groups

HAVING
→ filters groups
```

### And always ask:

> **What should one output row represent?**

If the answer is:

```text
one customer
→ GROUP BY customer_id

one department
→ GROUP BY department_id

one category
→ GROUP BY category_id

one month
→ GROUP BY year/month
```

Once this way of thinking becomes natural, writing aggregation queries becomes much easier.