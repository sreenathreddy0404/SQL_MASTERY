# PHASE 6 — COMPLEX SQL PROBLEM SOLVING

## 1. What is Phase 6?

In the previous phases, you learned individual SQL concepts:

- SELECT
- WHERE
- JOIN
- GROUP BY
- HAVING
- Subqueries
- CTEs
- Window functions

In Phase 6, the goal is to **combine these concepts to solve real-world SQL problems**.

The main skill is:

> Convert a business requirement written in English into a correct SQL query.

For example:

> Find the top 3 customers in every state based on completed-order spending.

This requires:

- JOIN
- WHERE
- GROUP BY
- SUM
- CTE
- ROW_NUMBER
- PARTITION BY

So Phase 6 is where SQL starts becoming **interview-level and production-style SQL**.

---

# 2. CASE Expression

`CASE` is used when you want to create categories or apply conditional logic.

### Syntax

```sql
CASE
    WHEN condition THEN result
    WHEN condition THEN result
    ELSE result
END
```

### Example

Classify products according to price:

```sql
SELECT
    product_name,
    price,
    CASE
        WHEN price >= 50000 THEN 'Expensive'
        WHEN price >= 10000 THEN 'Medium'
        ELSE 'Affordable'
    END AS price_category
FROM products;
```

### How it works

For every product:

```text
price >= 50000 → Expensive
price >= 10000 → Medium
otherwise      → Affordable
```

`CASE` works row by row.

---

# 3. CASE with Aggregation

`CASE` becomes very powerful when combined with aggregate functions.

Example:

> Count employees whose salary is at least 100000.

```sql
SELECT
    SUM(
        CASE
            WHEN salary >= 100000 THEN 1
            ELSE 0
        END
    ) AS high_salary_employees
FROM employees;
```

For every employee:

```text
salary >= 100000 → 1
otherwise        → 0
```

Then:

```text
SUM(1 + 0 + 1 + 0 + ...)
```

gives the count.

This technique is called **conditional aggregation**.

---

# 4. Multiple Conditional Aggregations

You can calculate multiple categories in one query.

```sql
SELECT
    SUM(CASE
        WHEN salary < 70000 THEN 1
        ELSE 0
    END) AS low_salary,

    SUM(CASE
        WHEN salary >= 70000 AND salary < 100000 THEN 1
        ELSE 0
    END) AS medium_salary,

    SUM(CASE
        WHEN salary >= 100000 THEN 1
        ELSE 0
    END) AS high_salary
FROM employees;
```

This is extremely useful in reporting queries.

---

# 5. CASE in ORDER BY

You can also define your own sorting priority.

Suppose the business wants:

```text
Completed
Pending
Cancelled
```

in that order.

```sql
SELECT *
FROM orders
ORDER BY
    CASE
        WHEN status = 'Completed' THEN 1
        WHEN status = 'Pending' THEN 2
        WHEN status = 'Cancelled' THEN 3
    END;
```

This is useful when normal alphabetical sorting isn't what the business wants.

---

# 6. Date Functions

MySQL provides many functions for working with dates.

Important functions:

```text
YEAR()
MONTH()
DAY()
CURDATE()
NOW()
DATEDIFF()
DATE_ADD()
DATE_SUB()
LAST_DAY()
DAYNAME()
MONTHNAME()
```

---

# 7. YEAR(), MONTH(), DAY()

```sql
SELECT
    order_date,
    YEAR(order_date) AS year,
    MONTH(order_date) AS month,
    DAY(order_date) AS day
FROM orders;
```

For:

```text
2024-03-15
```

the result is:

```text
year  = 2024
month = 3
day   = 15
```

---

# 8. Monthly Order Report

Suppose the interviewer asks:

> Find the number of orders placed each month.

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

The important idea is:

```text
date
 ↓
YEAR + MONTH
 ↓
GROUP BY
 ↓
COUNT
```

---

# 9. DATEDIFF()

`DATEDIFF()` calculates the number of days between two dates.

```sql
SELECT
    name,
    hire_date,
    DATEDIFF(CURDATE(), hire_date) AS days_worked
FROM employees;
```

This can answer questions such as:

> How many days has each employee worked?

---

# 10. DATE_ADD() and DATE_SUB()

Add days:

```sql
SELECT
    DATE_ADD(order_date, INTERVAL 7 DAY)
FROM orders;
```

Subtract days:

```sql
SELECT
    DATE_SUB(order_date, INTERVAL 7 DAY)
FROM orders;
```

These are useful for date ranges and reporting.

---

# 11. String Functions

Important MySQL string functions:

```text
CONCAT()
CONCAT_WS()
UPPER()
LOWER()
LENGTH()
TRIM()
SUBSTRING()
LEFT()
RIGHT()
REPLACE()
```

Example:

```sql
SELECT
    CONCAT(name, ' - ', city) AS customer_info
FROM customers;
```

Example:

```sql
SELECT
    UPPER(name) AS name
FROM customers;
```

Example:

```sql
SELECT
    LOWER(email) AS email
FROM customers;
```

---

# 12. NULL Handling

Important functions:

```text
COALESCE()
IFNULL()
NULLIF()
```

### COALESCE()

Returns the first non-NULL value.

```sql
SELECT
    name,
    COALESCE(city, 'Unknown') AS city
FROM customers;
```

If city is NULL:

```text
NULL → Unknown
```

### IFNULL()

MySQL-specific two-argument function:

```sql
SELECT
    name,
    IFNULL(city, 'Unknown') AS city
FROM customers;
```

### NULLIF()

Returns NULL if the two values are equal.

```sql
SELECT NULLIF(10, 10);
```

Result:

```text
NULL
```

---

# 13. JOIN + GROUP BY + HAVING

Now we start combining concepts.

Question:

> Find each customer's completed-order spending.

```sql
SELECT
    c.customer_id,
    c.name,
    COUNT(o.order_id) AS completed_orders,
    SUM(o.total_amount) AS total_spent,
    AVG(o.total_amount) AS avg_order_value
FROM customers c
JOIN orders o
    ON c.customer_id = o.customer_id
WHERE o.status = 'Completed'
GROUP BY
    c.customer_id,
    c.name;
```

Logical process:

```text
customers
   ↓
JOIN orders
   ↓
keep Completed orders
   ↓
GROUP BY customer
   ↓
COUNT / SUM / AVG
```

---

# 14. WHERE vs HAVING in Complex Queries

Suppose:

> Find customers whose completed-order spending is greater than 50000.

```sql
SELECT
    c.customer_id,
    c.name,
    COUNT(o.order_id) AS completed_orders,
    SUM(o.total_amount) AS total_spent
FROM customers c
JOIN orders o
    ON c.customer_id = o.customer_id
WHERE o.status = 'Completed'
GROUP BY
    c.customer_id,
    c.name
HAVING SUM(o.total_amount) > 50000
ORDER BY total_spent DESC;
```

Remember:

```text
WHERE  → filters rows
HAVING → filters groups
```

---

# 15. CASE + Aggregation + GROUP BY

You can classify customers based on their spending.

For example:

```text
>= 100000 → VIP
>= 50000  → Regular
otherwise → Low Value
```

```sql
SELECT
    c.customer_id,
    c.name,
    SUM(o.total_amount) AS total_spent,
    CASE
        WHEN SUM(o.total_amount) >= 100000 THEN 'VIP'
        WHEN SUM(o.total_amount) >= 50000 THEN 'Regular'
        ELSE 'Low Value'
    END AS customer_type
FROM customers c
JOIN orders o
    ON c.customer_id = o.customer_id
WHERE o.status = 'Completed'
GROUP BY
    c.customer_id,
    c.name;
```

Notice that `CASE` is using the result of:

```sql
SUM(o.total_amount)
```

---

# 16. Joining Multiple Tables

Real-world queries often require multiple joins.

Example:

> Show customer, order date, product and quantity.

```sql
SELECT
    c.name,
    o.order_date,
    p.product_name,
    oi.quantity,
    oi.unit_price
FROM customers c
JOIN orders o
    ON c.customer_id = o.customer_id
JOIN order_items oi
    ON o.order_id = oi.order_id
JOIN products p
    ON oi.product_id = p.product_id;
```

Relationship path:

```text
customers
    ↓
orders
    ↓
order_items
    ↓
products
```

Always identify this path before writing the query.

---

# 17. Product Revenue

Question:

> Find total revenue generated by each product.

```sql
SELECT
    p.product_id,
    p.product_name,
    SUM(oi.quantity * oi.unit_price) AS revenue
FROM products p
JOIN order_items oi
    ON p.product_id = oi.product_id
JOIN orders o
    ON oi.order_id = o.order_id
WHERE o.status = 'Completed'
GROUP BY
    p.product_id,
    p.product_name
ORDER BY revenue DESC;
```

Important:

Use:

```sql
oi.unit_price
```

instead of:

```sql
p.price
```

because `order_items.unit_price` represents the historical transaction price.

The current product price may change later.

---

# 18. Category Revenue

```sql
SELECT
    c.category_name,
    SUM(oi.quantity * oi.unit_price) AS revenue
FROM categories c
JOIN products p
    ON c.category_id = p.category_id
JOIN order_items oi
    ON p.product_id = oi.product_id
JOIN orders o
    ON oi.order_id = o.order_id
WHERE o.status = 'Completed'
GROUP BY
    c.category_id,
    c.category_name
ORDER BY revenue DESC;
```

Relationship:

```text
category
   ↓
products
   ↓
order_items
   ↓
orders
```

---

# 19. Monthly Revenue

```sql
SELECT
    YEAR(o.order_date) AS year,
    MONTH(o.order_date) AS month,
    SUM(oi.quantity * oi.unit_price) AS revenue
FROM orders o
JOIN order_items oi
    ON o.order_id = oi.order_id
WHERE o.status = 'Completed'
GROUP BY
    YEAR(o.order_date),
    MONTH(o.order_date)
ORDER BY
    year,
    month;
```

This combines:

```text
JOIN
+
WHERE
+
YEAR/MONTH
+
GROUP BY
+
SUM
```

---

# 20. CTE + Aggregation + Window Function

Now we move to more advanced problems.

Question:

> Find the top 3 customers in each state based on completed spending.

First calculate spending:

```sql
WITH customer_spending AS (
    SELECT
        c.customer_id,
        c.name,
        c.state,
        SUM(o.total_amount) AS total_spent
    FROM customers c
    JOIN orders o
        ON c.customer_id = o.customer_id
    WHERE o.status = 'Completed'
    GROUP BY
        c.customer_id,
        c.name,
        c.state
)
```

Then rank customers:

```sql
WITH customer_spending AS (
    SELECT
        c.customer_id,
        c.name,
        c.state,
        SUM(o.total_amount) AS total_spent
    FROM customers c
    JOIN orders o
        ON c.customer_id = o.customer_id
    WHERE o.status = 'Completed'
    GROUP BY
        c.customer_id,
        c.name,
        c.state
),
ranked_customers AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY state
            ORDER BY total_spent DESC
        ) AS rn
    FROM customer_spending
)
SELECT *
FROM ranked_customers
WHERE rn <= 3;
```

This is a very important interview pattern:

```text
GROUP BY
    ↓
calculate metric
    ↓
ROW_NUMBER()
    ↓
PARTITION BY
    ↓
filter rank
```

---

# 21. Top N Per Group

The general pattern is:

```sql
WITH ranked AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY group_column
            ORDER BY value DESC
        ) AS rn
    FROM table_name
)
SELECT *
FROM ranked
WHERE rn <= N;
```

Example:

> Top 2 products by price in every category.

```sql
WITH ranked_products AS (
    SELECT
        p.product_id,
        p.product_name,
        p.category_id,
        p.price,
        ROW_NUMBER() OVER (
            PARTITION BY p.category_id
            ORDER BY p.price DESC
        ) AS rn
    FROM products p
)
SELECT *
FROM ranked_products
WHERE rn <= 2;
```

---

# 22. Why CTE Is Needed for Window Filtering

This does not normally work:

```sql
SELECT
    name,
    ROW_NUMBER() OVER (ORDER BY salary DESC) AS rn
FROM employees
WHERE rn <= 3;
```

Why?

Because `rn` is generated by the window function after the `WHERE` stage.

Instead:

```sql
WITH ranked AS (
    SELECT
        name,
        salary,
        ROW_NUMBER() OVER (
            ORDER BY salary DESC
        ) AS rn
    FROM employees
)
SELECT *
FROM ranked
WHERE rn <= 3;
```

Think:

```text
inner query
    ↓
calculate rank
    ↓
outer query
    ↓
filter rank
```

---

# 23. Products Never Ordered

Question:

> Find products that have never been ordered.

Use `LEFT JOIN`:

```sql
SELECT
    p.product_id,
    p.product_name
FROM products p
LEFT JOIN order_items oi
    ON p.product_id = oi.product_id
WHERE oi.product_id IS NULL;
```

Mental model:

```text
LEFT JOIN
+
right side NULL
=
no matching record
```

Another valid approach is `NOT EXISTS`.

---

# 24. Customers Who Bought a Specific Product

Question:

> Find customers who purchased the Laptop.

```sql
SELECT DISTINCT
    c.customer_id,
    c.name
FROM customers c
JOIN orders o
    ON c.customer_id = o.customer_id
JOIN order_items oi
    ON o.order_id = oi.order_id
JOIN products p
    ON oi.product_id = p.product_id
WHERE p.product_name = 'Laptop';
```

Why `DISTINCT`?

A customer may have purchased the same product multiple times.

---

# 25. Customers Who Purchased Multiple Different Products

```sql
SELECT
    c.customer_id,
    c.name,
    COUNT(DISTINCT oi.product_id) AS different_products
FROM customers c
JOIN orders o
    ON c.customer_id = o.customer_id
JOIN order_items oi
    ON o.order_id = oi.order_id
GROUP BY
    c.customer_id,
    c.name
HAVING COUNT(DISTINCT oi.product_id) >= 2;
```

Important:

```sql
COUNT(*)
```

counts rows.

```sql
COUNT(DISTINCT product_id)
```

counts different products.

---

# 26. Running Total

Question:

> Show monthly revenue and cumulative revenue.

First calculate monthly revenue:

```sql
WITH monthly_revenue AS (
    SELECT
        YEAR(o.order_date) AS year,
        MONTH(o.order_date) AS month,
        SUM(oi.quantity * oi.unit_price) AS revenue
    FROM orders o
    JOIN order_items oi
        ON o.order_id = oi.order_id
    WHERE o.status = 'Completed'
    GROUP BY
        YEAR(o.order_date),
        MONTH(o.order_date)
)
SELECT
    year,
    month,
    revenue,
    SUM(revenue) OVER (
        ORDER BY year, month
    ) AS running_revenue
FROM monthly_revenue
ORDER BY year, month;
```

The window function:

```sql
SUM(revenue) OVER (
    ORDER BY year, month
)
```

keeps accumulating the previous values.

---

# 27. Running Total Per Customer

Suppose we want each customer's cumulative spending.

```sql
SELECT
    customer_id,
    order_date,
    total_amount,
    SUM(total_amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS running_spending
FROM orders
WHERE status = 'Completed';
```

Important:

```text
PARTITION BY customer_id
```

means each customer gets a separate calculation.

---

# 28. Previous Order Using LAG()

`LAG()` gives the previous row's value.

```sql
SELECT
    customer_id,
    order_date,
    total_amount,
    LAG(total_amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS previous_order_amount
FROM orders;
```

This allows questions such as:

> How much did the customer spend on their previous order?

---

# 29. Next Order Using LEAD()

```sql
SELECT
    customer_id,
    order_date,
    LEAD(order_date) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS next_order_date
FROM orders;
```

`LEAD()` looks forward.

```text
LAG  → previous
LEAD → next
```

---

# 30. Repeat Customers

Question:

> Find customers who placed more than one order.

```sql
SELECT
    customer_id,
    COUNT(*) AS order_count
FROM orders
GROUP BY customer_id
HAVING COUNT(*) > 1;
```

If the business definition is:

> Customer with more than one completed order

then:

```sql
SELECT
    customer_id,
    COUNT(*) AS completed_orders
FROM orders
WHERE status = 'Completed'
GROUP BY customer_id
HAVING COUNT(*) > 1;
```

Always pay attention to the business definition.

---

# 31. Customers Active in at Least 3 Months

```sql
SELECT
    customer_id,
    COUNT(
        DISTINCT DATE_FORMAT(order_date, '%Y-%m')
    ) AS active_months
FROM orders
GROUP BY customer_id
HAVING COUNT(
    DISTINCT DATE_FORMAT(order_date, '%Y-%m')
) >= 3;
```

The important concept is:

```text
date
 ↓
year-month
 ↓
DISTINCT
 ↓
COUNT
```

---

# 32. Completed Order Percentage

Conditional aggregation can calculate percentages.

```sql
SELECT
    100.0 *
    SUM(
        CASE
            WHEN status = 'Completed' THEN 1
            ELSE 0
        END
    ) / COUNT(*) AS completed_percentage
FROM orders;
```

The numerator:

```text
number of completed orders
```

The denominator:

```text
total orders
```

---

# 33. Completed Revenue Percentage

```sql
SELECT
    100.0 *
    SUM(
        CASE
            WHEN status = 'Completed'
            THEN total_amount
            ELSE 0
        END
    )
    / NULLIF(SUM(total_amount), 0)
    AS completed_revenue_percentage
FROM orders;
```

`NULLIF()` prevents division by zero.

This is an important production habit:

> Protect calculations against invalid edge cases.

---

# 34. Above-Average Customer Spending

Question:

> Find customers whose spending is above the average customer spending.

First calculate customer spending:

```sql
WITH customer_spending AS (
    SELECT
        customer_id,
        SUM(total_amount) AS total_spent
    FROM orders
    WHERE status = 'Completed'
    GROUP BY customer_id
),
average_spending AS (
    SELECT
        AVG(total_spent) AS avg_spent
    FROM customer_spending
)
SELECT
    cs.customer_id,
    cs.total_spent
FROM customer_spending cs
CROSS JOIN average_spending a
WHERE cs.total_spent > a.avg_spent;
```

Logical process:

```text
orders
 ↓
customer spending
 ↓
average customer spending
 ↓
compare each customer
 ↓
return above-average customers
```

This is an example of **multi-stage SQL problem solving**.

---

# 35. The Most Important Skill: Identify the Output Grain

Before writing SQL, ask:

> What does ONE ROW in my final result represent?

Examples:

```text
one row per customer
one row per product
one row per category
one row per department
one row per month
one row per customer per month
one row per employee
```

For example:

> Find total spending for each customer.

Output grain:

```text
one row = one customer
```

Therefore:

```sql
GROUP BY customer_id
```

For:

> Find monthly revenue.

Output grain:

```text
one row = one month
```

Therefore:

```sql
GROUP BY YEAR(order_date), MONTH(order_date)
```

For:

> Top 3 customers in every state.

Output grain:

```text
one row = one customer
but ranking is separated by state
```

Therefore:

```sql
PARTITION BY state
```

This is one of the most important SQL problem-solving skills.

---

# 36. English → SQL Translation

Learn to translate common phrases.

### "For each"

Usually means:

```sql
GROUP BY
```

Example:

> Total revenue for each category.

```sql
GROUP BY category
```

---

### "More than"

If it applies to individual rows:

```sql
WHERE
```

If it applies to aggregated results:

```sql
HAVING
```

Example:

> Employees earning more than 80000.

```sql
WHERE salary > 80000
```

Example:

> Departments whose average salary is greater than 80000.

```sql
HAVING AVG(salary) > 80000
```

---

### "Top 5 overall"

Usually:

```sql
ORDER BY ... DESC
LIMIT 5
```

---

### "Top 5 per department"

Usually:

```sql
ROW_NUMBER()
OVER (
    PARTITION BY department
    ORDER BY value DESC
)
```

---

### "Above average"

Usually requires:

```text
subquery
or
CTE
or
window function
```

---

### "Previous"

Think:

```sql
LAG()
```

---

### "Next"

Think:

```sql
LEAD()
```

---

### "Running"

Think:

```sql
SUM() OVER()
```

---

### "Never"

Think:

```text
NOT EXISTS
```

or:

```sql
LEFT JOIN ... IS NULL
```

---

# 37. Complex SQL Problem-Solving Framework

When you receive a difficult SQL problem, follow these steps.

## Step 1 — Identify the output grain

Ask:

```text
What does one result row represent?
```

---

## Step 2 — Identify the required tables

Example:

```text
customer name
order date
product name
quantity
```

requires:

```text
customers
orders
order_items
products
```

---

## Step 3 — Find the relationship path

```text
customers
    ↓
orders
    ↓
order_items
    ↓
products
```

---

## Step 4 — Apply row-level filters

Use:

```sql
WHERE
```

Example:

```sql
WHERE o.status = 'Completed'
```

---

## Step 5 — Determine the required aggregation

Ask:

```text
Do I need COUNT?
SUM?
AVG?
MIN?
MAX?
COUNT(DISTINCT)?
```

---

## Step 6 — GROUP BY

Group according to the required output grain.

---

## Step 7 — HAVING

If you need to filter aggregated results:

```sql
HAVING
```

---

## Step 8 — Decide whether a window function is needed

Use window functions for:

```text
ranking
top N per group
running totals
previous row
next row
comparisons
```

---

## Step 9 — Use CTE/Subquery if there are multiple stages

If the problem sounds like:

```text
calculate X
then calculate Y from X
then filter based on Y
```

a CTE often makes the solution much clearer.

---

# 38. Core Phase 6 Patterns

### Pattern 1 — Conditional logic

```sql
CASE
    WHEN condition THEN result
    ELSE result
END
```

### Pattern 2 — Conditional aggregation

```sql
SUM(
    CASE
        WHEN condition THEN value
        ELSE 0
    END
)
```

### Pattern 3 — Top N per group

```sql
WITH ranked AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY group_column
            ORDER BY value DESC
        ) AS rn
    FROM table_name
)
SELECT *
FROM ranked
WHERE rn <= N;
```

### Pattern 4 — Monthly report

```sql
SELECT
    YEAR(date_column),
    MONTH(date_column),
    SUM(value)
FROM table_name
GROUP BY
    YEAR(date_column),
    MONTH(date_column);
```

### Pattern 5 — Running total

```sql
SUM(value) OVER (
    ORDER BY date_column
)
```

### Pattern 6 — Running total per group

```sql
SUM(value) OVER (
    PARTITION BY group_column
    ORDER BY date_column
)
```

### Pattern 7 — Previous row

```sql
LAG(value) OVER (
    ORDER BY date_column
)
```

### Pattern 8 — Next row

```sql
LEAD(value) OVER (
    ORDER BY date_column
)
```

### Pattern 9 — Find records with no match

```sql
LEFT JOIN other_table
    ON ...
WHERE other_table.id IS NULL;
```

or:

```sql
WHERE NOT EXISTS (...)
```

---

# 39. Phase 6 Interview-Level Practice

## Level 1 — CASE and Conditional Aggregation

1. Classify products as Expensive, Medium, or Affordable.
2. Classify employees based on salary.
3. Classify products based on stock level.
4. Count high-salary employees in each department.
5. Calculate completed, pending, and cancelled order revenue using conditional aggregation.

## Level 2 — Dates

6. Find the number of orders placed each month.
7. Find monthly completed revenue.
8. Find the number of customers who signed up each month.
9. Find employees hired in each year.
10. Find employees who have worked for more than 1000 days.
11. Find orders placed during January 2024.
12. Find the number of days between order date and payment date.

## Level 3 — Business Aggregation

13. Find total spending of every customer.
14. Find completed spending of every customer.
15. Find customers whose completed spending is greater than 50000.
16. Find the top 5 customers by completed spending.
17. Find total quantity sold for every product.
18. Find revenue generated by every product.
19. Find revenue generated by every category.
20. Find the top 3 products by revenue.

## Level 4 — Advanced Business Problems

21. Find the top 2 products by revenue in every category.
22. Find the highest-paid employee in every department.
23. Find employees earning more than their department's average salary.
24. Find customers whose spending is above average customer spending.
25. Find customers who purchased at least 3 different products.
26. Find products that have never been ordered.
27. Find customers who never placed a completed order.
28. Find customers who placed orders in at least 3 different months.
29. Find the month with the highest completed revenue.
30. Find the category with the highest average product price.

## Level 5 — Interview-Level Problems

31. Find the top 3 customers by spending in each state.
32. Find the second-highest-paid employee in each department.
33. Find products whose revenue is greater than average product revenue.
34. Find the first order date for every customer.
35. Find the latest order date for every customer.
36. Find the difference between each customer's current order and previous order.
37. Find the running spending total for every customer.
38. Find monthly revenue and month-over-month revenue difference.
39. Find monthly revenue and month-over-month percentage growth.
40. Find customers whose latest order was completed.
41. Find customers who purchased every product in a category.
42. Find the most frequently purchased product.
43. Find the customer who purchased the greatest number of different products.
44. Find the department with the highest average salary.
45. Find employees who earn more than their manager.

---

# 40. Phase 6 Mastery Checklist

Before moving to Phase 7, you should be comfortable with:

```text
☐ CASE
☐ Conditional aggregation
☐ Date functions
☐ String functions
☐ NULL handling
☐ JOIN + GROUP BY
☐ JOIN + HAVING
☐ Multiple-table joins
☐ Business calculations
☐ CTE + aggregation
☐ CTE + window functions
☐ ROW_NUMBER
☐ RANK
☐ DENSE_RANK
☐ LAG
☐ LEAD
☐ Running totals
☐ Top N per group
☐ Above-average problems
☐ First/last record problems
☐ "Never" problems
☐ COUNT(DISTINCT)
☐ Percentage calculations
☐ English → SQL translation
☐ Identifying output grain
☐ Breaking complex problems into stages
```

## Phase 6 Mental Model

The most important progression is:

```text
Simple SQL
   ↓
JOIN
   ↓
JOIN + FILTER
   ↓
JOIN + GROUP BY
   ↓
JOIN + GROUP BY + HAVING
   ↓
CTE / Subquery
   ↓
Window Functions
   ↓
CTE + Window Functions
   ↓
Multiple-stage business query
   ↓
Interview-level SQL
```

The goal of Phase 6 is **not to memorize 45 queries**.

The goal is to look at a new problem and think:

```text
What should one row represent?
        ↓
Which tables do I need?
        ↓
How are they connected?
        ↓
Which rows should I filter?
        ↓
Do I need aggregation?
        ↓
What should I GROUP BY?
        ↓
Do I need HAVING?
        ↓
Do I need ranking/window functions?
        ↓
Do I need a CTE or subquery?
        ↓
Write the query
```

Once this thought process becomes natural, you can solve SQL problems that you have never seen before.