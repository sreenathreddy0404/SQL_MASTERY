# PHASE 5 — SUBQUERIES, CTEs & WINDOW FUNCTIONS

## 1. PHASE 5 OVERVIEW

Phase 5 focuses on solving SQL problems where basic SELECT, WHERE, JOIN, GROUP BY, and HAVING are not enough.

Main topics:

1. Subqueries
2. IN and NOT IN
3. EXISTS and NOT EXISTS
4. Correlated Subqueries
5. ANY and ALL
6. Common Table Expressions (CTEs)
7. Multiple CTEs
8. Recursive CTEs
9. Window Functions
10. ROW_NUMBER()
11. RANK()
12. DENSE_RANK()
13. LAG()
14. LEAD()
15. Aggregate Window Functions
16. PARTITION BY
17. Running Totals
18. Top-N-per-Group Problems

---

# PART 1 — SUBQUERIES

## 2. WHAT IS A SUBQUERY?

A subquery is a SQL query written inside another SQL query.

Example:

```sql
SELECT *
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

The inner query:

```sql
SELECT AVG(salary)
FROM employees;
```

calculates the average salary.

The outer query finds employees whose salary is greater than that average.

Mental model:

```text
Outer Query
     ↓
Uses result of
     ↓
Inner Query
```

---

# 3. SCALAR SUBQUERY

A scalar subquery returns exactly one value.

Example:

```sql
SELECT MAX(salary)
FROM employees;
```

This returns one value.

We can use it like:

```sql
SELECT *
FROM employees
WHERE salary = (
    SELECT MAX(salary)
    FROM employees
);
```

This finds employees with the highest salary.

Another example:

```sql
SELECT *
FROM products
WHERE price > (
    SELECT AVG(price)
    FROM products
);
```

This finds products whose price is greater than the average product price.

---

# 4. SUBQUERY IN SELECT

A subquery can also be placed inside SELECT.

```sql
SELECT
    name,
    salary,
    (
        SELECT AVG(salary)
        FROM employees
    ) AS company_avg_salary
FROM employees;
```

Every employee is displayed along with the overall average salary.

---

# 5. SUBQUERY IN FROM

A subquery can be used like a temporary table.

```sql
SELECT *
FROM (
    SELECT
        department_id,
        AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
) AS dept_salary;
```

The inner query produces a result.

The outer query treats that result like a table.

Important:

A subquery used in FROM needs an alias.

```sql
) AS dept_salary
```

---

# 6. SUBQUERY WITH GROUP BY

Example:

Find departments whose average salary is greater than 80,000.

```sql
SELECT *
FROM (
    SELECT
        department_id,
        AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
) AS dept_salary
WHERE avg_salary > 80000;
```

The inner query creates department-level averages.

The outer query filters those results.

---

# 7. MULTI-ROW SUBQUERY

A subquery can return multiple rows.

Example:

```sql
SELECT customer_id
FROM orders;
```

This can return many customer IDs.

We can use it with IN:

```sql
SELECT *
FROM customers
WHERE customer_id IN (
    SELECT customer_id
    FROM orders
);
```

Meaning:

Find customers whose ID exists in the orders table.

---

# 8. IN WITH SUBQUERY

General pattern:

```sql
SELECT ...
FROM table
WHERE column IN (
    SELECT column
    FROM another_table
);
```

Example:

```sql
SELECT *
FROM products
WHERE product_id IN (
    SELECT product_id
    FROM order_items
);
```

This finds products that have appeared in orders.

---

# 9. NOT IN

`NOT IN` checks that a value does not exist in the returned set.

Example:

```sql
SELECT *
FROM customers
WHERE customer_id NOT IN (
    SELECT customer_id
    FROM orders
);
```

Meaning:

Find customers who have never placed an order.

Important:

`NOT IN` can produce unexpected results when the subquery contains NULL.

For NULL-safe existence checks, `NOT EXISTS` is often preferable.

---

# 10. EXISTS

`EXISTS` checks whether at least one matching row exists.

Example:

```sql
SELECT *
FROM customers c
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
);
```

Meaning:

Return customers for whom at least one order exists.

The result of the inner query itself is not important.

Only existence matters.

---

# 11. WHY SELECT 1 WITH EXISTS?

We commonly write:

```sql
SELECT 1
FROM orders
WHERE ...
```

inside EXISTS.

`EXISTS` only asks:

```text
Does a matching row exist?
```

It does not care what columns are selected.

Therefore:

```sql
SELECT 1
```

clearly communicates the intention.

---

# 12. NOT EXISTS

`NOT EXISTS` checks that no matching row exists.

Example:

```sql
SELECT *
FROM customers c
WHERE NOT EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
);
```

Meaning:

Find customers who have never placed an order.

Mental model:

```text
EXISTS
    → at least one match

NOT EXISTS
    → no match
```

---

# 13. IN VS EXISTS

IN:

```sql
WHERE customer_id IN (
    SELECT customer_id
    FROM orders
)
```

Think:

```text
Is this value present in the returned set?
```

EXISTS:

```sql
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
)
```

Think:

```text
Does a matching row exist?
```

Interview answer:

`IN` compares a value against a set of values, while `EXISTS` checks whether a matching row exists.

---

# 14. CORRELATED SUBQUERY

A correlated subquery references a column from the outer query.

Example:

Find employees earning more than the average salary of their own department.

```sql
SELECT
    e.employee_id,
    e.name,
    e.department_id,
    e.salary
FROM employees e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.department_id = e.department_id
);
```

The important part is:

```sql
WHERE e2.department_id = e.department_id
```

`e.department_id` belongs to the outer query.

Therefore the inner query depends on the current employee.

---

# 15. NORMAL VS CORRELATED SUBQUERY

Normal subquery:

```sql
SELECT *
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

Compares against one overall average.

Correlated subquery:

```sql
SELECT *
FROM employees e
WHERE salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.department_id = e.department_id
);
```

Compares against the employee's department average.

Remember:

```text
Normal subquery
    → independent result

Correlated subquery
    → depends on outer row
```

---

# 16. ANY

`ANY` means the condition must be true for at least one value returned by the subquery.

Example:

```sql
SELECT *
FROM employees
WHERE salary > ANY (
    SELECT salary
    FROM employees
    WHERE department_id = 2
);
```

Meaning:

Salary must be greater than at least one salary from department 2.

Think:

```text
> ANY
→ greater than at least one
```

---

# 17. ALL

`ALL` means the condition must be true for every value returned.

```sql
SELECT *
FROM employees
WHERE salary > ALL (
    SELECT salary
    FROM employees
    WHERE department_id = 2
);
```

Meaning:

Salary must be greater than every employee's salary in department 2.

Think:

```text
> ANY
→ greater than at least one

> ALL
→ greater than every one
```

---

# PART 2 — COMMON TABLE EXPRESSIONS

## 18. WHAT IS A CTE?

CTE means:

Common Table Expression.

It allows us to create a named temporary result using WITH.

Syntax:

```sql
WITH cte_name AS (
    SELECT ...
)
SELECT ...
FROM cte_name;
```

Example:

```sql
WITH dept_salary AS (
    SELECT
        department_id,
        AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
)
SELECT *
FROM dept_salary;
```

---

# 19. WHY USE CTEs?

CTEs make complex SQL easier to read and maintain.

Without CTE:

```sql
SELECT *
FROM (
    SELECT
        department_id,
        AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
) AS dept_salary
WHERE avg_salary > 80000;
```

With CTE:

```sql
WITH dept_salary AS (
    SELECT
        department_id,
        AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
)
SELECT *
FROM dept_salary
WHERE avg_salary > 80000;
```

The second version is easier to understand.

---

# 20. CTE WITH FILTERING

```sql
WITH high_salary AS (
    SELECT *
    FROM employees
    WHERE salary > 80000
)
SELECT *
FROM high_salary;
```

The CTE creates the intermediate result.

The main query uses it.

---

# 21. CTE WITH AGGREGATION

Find departments having more than two employees.

```sql
WITH dept_count AS (
    SELECT
        department_id,
        COUNT(*) AS employee_count
    FROM employees
    GROUP BY department_id
)
SELECT *
FROM dept_count
WHERE employee_count > 2;
```

---

# 22. CTE WITH JOIN

Find customer spending.

```sql
WITH customer_spending AS (
    SELECT
        customer_id,
        SUM(total_amount) AS total_spent
    FROM orders
    GROUP BY customer_id
)
SELECT
    c.name,
    cs.total_spent
FROM customers c
JOIN customer_spending cs
    ON c.customer_id = cs.customer_id;
```

The CTE calculates spending.

The main query adds customer information.

---

# 23. MULTIPLE CTEs

Multiple CTEs can be defined using commas.

```sql
WITH customer_spending AS (
    SELECT
        customer_id,
        SUM(total_amount) AS total_spent
    FROM orders
    GROUP BY customer_id
),
customer_orders AS (
    SELECT
        customer_id,
        COUNT(*) AS order_count
    FROM orders
    GROUP BY customer_id
)
SELECT
    c.name,
    cs.total_spent,
    co.order_count
FROM customers c
JOIN customer_spending cs
    ON c.customer_id = cs.customer_id
JOIN customer_orders co
    ON c.customer_id = co.customer_id;
```

Mental model:

```text
CTE 1
  ↓
CTE 2
  ↓
Main Query
```

---

# 24. CTE PROBLEM-SOLVING METHOD

When a query becomes complex, break it into logical steps.

Example:

```text
Step 1 → Calculate customer spending

Step 2 → Calculate customer order count

Step 3 → Join both results

Step 4 → Add customer details

Step 5 → Filter/sort final result
```

Each logical step can become a CTE.

---

# PART 3 — RECURSIVE CTE

## 25. WHAT IS A RECURSIVE CTE?

A recursive CTE can reference itself.

It is useful for:

- Hierarchies
- Employee-manager relationships
- Organization trees
- Category trees
- Graph-like structures
- Generating sequences

Syntax:

```sql
WITH RECURSIVE cte_name AS (

    -- Anchor query

    UNION ALL

    -- Recursive query
)
SELECT *
FROM cte_name;
```

There are two important parts:

```text
Anchor
   ↓
Starting rows

Recursive part
   ↓
Generates next rows
```

---

# 26. SIMPLE RECURSIVE CTE

Generate numbers 1 to 5:

```sql
WITH RECURSIVE numbers AS (
    SELECT 1 AS n

    UNION ALL

    SELECT n + 1
    FROM numbers
    WHERE n < 5
)
SELECT *
FROM numbers;
```

Output:

```text
1
2
3
4
5
```

---

# 27. EMPLOYEE HIERARCHY

Our employees table contains:

```text
employee_id
manager_id
```

This creates a hierarchy.

Example:

```text
Amit
├── Ravi
│   ├── Neeraj
│   └── Karthik
└── Suresh
```

Recursive CTE:

```sql
WITH RECURSIVE employee_tree AS (

    SELECT
        employee_id,
        name,
        manager_id,
        0 AS level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    SELECT
        e.employee_id,
        e.name,
        e.manager_id,
        et.level + 1
    FROM employees e
    JOIN employee_tree et
        ON e.manager_id = et.employee_id
)
SELECT *
FROM employee_tree;
```

The `level` represents the hierarchy depth.

---

# PART 4 — WINDOW FUNCTIONS

## 28. WHAT IS A WINDOW FUNCTION?

A window function performs calculations across related rows without collapsing those rows.

This is the key difference from GROUP BY.

GROUP BY:

```text
Multiple rows
     ↓
One row per group
```

Window function:

```text
Multiple rows
     ↓
Keep all rows
     +
Calculate across related rows
```

---

# 29. BASIC WINDOW FUNCTION

Show every employee along with the overall average salary:

```sql
SELECT
    name,
    salary,
    AVG(salary) OVER () AS company_avg_salary
FROM employees;
```

Every employee remains in the result.

---

# 30. OVER()

The `OVER()` clause tells MySQL that the function is being used as a window function.

Example:

```sql
AVG(salary) OVER ()
```

means:

Calculate the average across the current result set without grouping the rows together.

---

# 31. PARTITION BY

`PARTITION BY` divides rows into separate windows.

Example:

```sql
SELECT
    name,
    department_id,
    salary,
    AVG(salary) OVER (
        PARTITION BY department_id
    ) AS dept_avg_salary
FROM employees;
```

Now each department gets its own average.

Every employee remains visible.

---

# 32. GROUP BY VS PARTITION BY

GROUP BY:

```sql
SELECT
    department_id,
    AVG(salary)
FROM employees
GROUP BY department_id;
```

Result:

```text
One row per department
```

PARTITION BY:

```sql
SELECT
    name,
    department_id,
    salary,
    AVG(salary) OVER (
        PARTITION BY department_id
    ) AS dept_avg
FROM employees;
```

Result:

```text
Every employee remains
+
Department average
```

Important:

```text
GROUP BY
→ collapses rows

PARTITION BY
→ does not collapse rows
```

---

# 33. ROW_NUMBER()

`ROW_NUMBER()` assigns a unique sequential number to each row.

```sql
SELECT
    name,
    salary,
    ROW_NUMBER() OVER (
        ORDER BY salary DESC
    ) AS row_num
FROM employees;
```

Highest salary gets row number 1.

---

# 34. ROW_NUMBER WITH PARTITION BY

Rank employees separately inside every department.

```sql
SELECT
    name,
    department_id,
    salary,
    ROW_NUMBER() OVER (
        PARTITION BY department_id
        ORDER BY salary DESC
    ) AS row_num
FROM employees;
```

Every department starts from 1.

---

# 35. RANK()

`RANK()` handles ties by giving equal rank.

Suppose:

```text
100000
90000
90000
80000
```

RANK:

```text
100000 → 1
90000  → 2
90000  → 2
80000  → 4
```

A gap appears after the tie.

---

# 36. DENSE_RANK()

`DENSE_RANK()` also gives equal rank to ties, but does not create gaps.

```text
100000 → 1
90000  → 2
90000  → 2
80000  → 3
```

---

# 37. ROW_NUMBER VS RANK VS DENSE_RANK

For:

```text
100000
90000
90000
80000
```

ROW_NUMBER:

```text
1
2
3
4
```

RANK:

```text
1
2
2
4
```

DENSE_RANK:

```text
1
2
2
3
```

Remember:

```text
ROW_NUMBER
→ always unique

RANK
→ ties + gaps

DENSE_RANK
→ ties + no gaps
```

---

# 38. TOP N PER GROUP

Very important interview problem.

Question:

Find the highest-paid employee in every department.

First rank employees:

```sql
WITH ranked_employees AS (
    SELECT
        name,
        department_id,
        salary,
        ROW_NUMBER() OVER (
            PARTITION BY department_id
            ORDER BY salary DESC
        ) AS rn
    FROM employees
)
SELECT *
FROM ranked_employees
WHERE rn = 1;
```

This pattern is extremely important.

---

# 39. TOP 2 PER GROUP

```sql
WITH ranked_employees AS (
    SELECT
        name,
        department_id,
        salary,
        ROW_NUMBER() OVER (
            PARTITION BY department_id
            ORDER BY salary DESC
        ) AS rn
    FROM employees
)
SELECT *
FROM ranked_employees
WHERE rn <= 2;
```

Pattern:

```text
CTE
 +
Window Function
 +
PARTITION BY
 +
Filter
```

---

# 40. LAG()

`LAG()` accesses a previous row.

Example:

```sql
SELECT
    order_date,
    total_amount,
    LAG(total_amount) OVER (
        ORDER BY order_date
    ) AS previous_amount
FROM orders;
```

Conceptually:

```text
amount    previous_amount
-------   ---------------
10000     NULL
15000     10000
12000     15000
```

Remember:

```text
LAG → previous row
```

---

# 41. LEAD()

`LEAD()` accesses the next row.

```sql
SELECT
    order_date,
    total_amount,
    LEAD(total_amount) OVER (
        ORDER BY order_date
    ) AS next_amount
FROM orders;
```

Remember:

```text
LEAD → next row
```

---

# 42. LAG WITH DIFFERENCE

```sql
SELECT
    order_date,
    total_amount,
    LAG(total_amount) OVER (
        ORDER BY order_date
    ) AS previous_amount,
    total_amount -
    LAG(total_amount) OVER (
        ORDER BY order_date
    ) AS difference
FROM orders;
```

Useful for:

- Month-over-month changes
- Salary changes
- Price changes
- Sales comparisons
- Time-series analysis

---

# 43. RUNNING TOTAL

A running total keeps accumulating values.

```sql
SELECT
    order_date,
    total_amount,
    SUM(total_amount) OVER (
        ORDER BY order_date
    ) AS running_total
FROM orders;
```

Conceptually:

```text
1000 → 1000
2000 → 3000
1500 → 4500
3000 → 7500
```

---

# 44. RUNNING TOTAL PER CUSTOMER

```sql
SELECT
    customer_id,
    order_date,
    total_amount,
    SUM(total_amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS running_total
FROM orders;
```

Each customer gets an independent running total.

---

# 45. RUNNING AVERAGE

```sql
SELECT
    order_date,
    total_amount,
    AVG(total_amount) OVER (
        ORDER BY order_date
    ) AS running_average
FROM orders;
```

---

# 46. COUNT AS WINDOW FUNCTION

```sql
SELECT
    name,
    department_id,
    COUNT(*) OVER (
        PARTITION BY department_id
    ) AS department_employee_count
FROM employees;
```

Every employee gets the number of employees in their department.

---

# 47. MULTIPLE WINDOW FUNCTIONS

You can use several window functions together.

```sql
SELECT
    name,
    department_id,
    salary,

    AVG(salary) OVER (
        PARTITION BY department_id
    ) AS dept_avg,

    RANK() OVER (
        PARTITION BY department_id
        ORDER BY salary DESC
    ) AS salary_rank,

    COUNT(*) OVER (
        PARTITION BY department_id
    ) AS dept_employee_count

FROM employees;
```

Each employee now has:

```text
Salary
Department average
Salary rank
Department employee count
```

---

# 48. WINDOW FUNCTION SYNTAX

General form:

```sql
FUNCTION(...) OVER (
    PARTITION BY column
    ORDER BY column
)
```

Examples:

```sql
AVG(salary) OVER (
    PARTITION BY department_id
)
```

```sql
ROW_NUMBER() OVER (
    ORDER BY salary DESC
)
```

```sql
SUM(total_amount) OVER (
    ORDER BY order_date
)
```

---

# 49. SUBQUERY VS CTE VS WINDOW FUNCTION

Use a:

### Subquery

When you need the result of one query inside another.

Example:

```text
salary > average salary
```

### CTE

When a query has multiple logical steps or an intermediate result needs a clear name.

Example:

```text
calculate spending
→ calculate order count
→ combine
```

### Window Function

When you need calculations across related rows while keeping individual rows.

Examples:

```text
ranking
running total
previous row
next row
department average beside employee
top N per group
```

---

# 50. IMPORTANT PHASE 5 MENTAL MODEL

```text
SUBQUERY
    ↓
Query inside another query

CTE
    ↓
Named intermediate result

WINDOW FUNCTION
    ↓
Calculation across rows
without collapsing them
```

---

# 51. IMPORTANT INTERVIEW DIFFERENCES

## WHERE vs HAVING

```text
WHERE
→ filters rows

HAVING
→ filters groups
```

## GROUP BY vs PARTITION BY

```text
GROUP BY
→ collapses rows

PARTITION BY
→ keeps rows
```

## IN vs EXISTS

```text
IN
→ checks value against a set

EXISTS
→ checks whether a matching row exists
```

## LAG vs LEAD

```text
LAG
→ previous row

LEAD
→ next row
```

## RANK vs DENSE_RANK

```text
RANK
→ ties create gaps

DENSE_RANK
→ ties don't create gaps
```

---

# 52. PHASE 5 QUERY PATTERNS

## Pattern 1 — Compare with aggregate

```sql
SELECT *
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

## Pattern 2 — EXISTS

```sql
SELECT *
FROM customers c
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
);
```

## Pattern 3 — CTE

```sql
WITH result AS (
    SELECT ...
)
SELECT *
FROM result;
```

## Pattern 4 — Multiple CTEs

```sql
WITH a AS (
    ...
),
b AS (
    ...
)
SELECT ...
FROM a
JOIN b
    ON ...;
```

## Pattern 5 — Ranking

```sql
ROW_NUMBER() OVER (
    PARTITION BY department_id
    ORDER BY salary DESC
)
```

## Pattern 6 — Previous row

```sql
LAG(value) OVER (
    ORDER BY date
)
```

## Pattern 7 — Next row

```sql
LEAD(value) OVER (
    ORDER BY date
)
```

## Pattern 8 — Running total

```sql
SUM(value) OVER (
    ORDER BY date
)
```

## Pattern 9 — Group calculation without collapsing rows

```sql
AVG(value) OVER (
    PARTITION BY group_column
)
```

---

# 53. PHASE 5 PRACTICE PROBLEMS

## Subqueries — Beginner

1. Find employees earning more than the overall average salary.

2. Find the employee(s) with the highest salary.

3. Find the employee(s) with the lowest salary.

4. Find products whose price is greater than the average product price.

5. Find customers who have placed at least one order using IN.

6. Find customers who have never placed an order using NOT IN.

## Subqueries — Intermediate

7. Find employees earning more than the average salary of their department.

8. Find products whose price is greater than the average price of their category.

9. Find customers whose total spending is greater than the average customer spending.

10. Find the second-highest salary using a subquery.

11. Find employees whose salary is greater than every employee in department 2.

12. Find employees whose salary is greater than at least one employee in department 2.

## EXISTS / NOT EXISTS

13. Find customers who have placed at least one order using EXISTS.

14. Find customers who have never placed an order using NOT EXISTS.

15. Find products that have appeared in at least one order.

16. Find products that have never been ordered.

17. Find customers who have at least one completed order.

## CTEs

18. Create a CTE containing department average salaries.

19. Using a CTE, find departments whose average salary is greater than 80,000.

20. Using a CTE, calculate total spending per customer.

21. Using a CTE, find customers who spent more than 50,000.

22. Create two CTEs:
    - customer order count
    - customer total spending

    Then combine them.

23. Using a CTE, find the top 3 customers by total spending.

## Recursive CTEs

24. Generate numbers from 1 to 10.

25. Generate numbers from 10 down to 1.

26. Generate dates for the next 7 days.

27. Traverse the employee-manager hierarchy.

28. Find all employees under a particular manager.

## Window Functions

29. Give every employee a row number ordered by salary descending.

30. Give every employee a row number within their department.

31. Rank employees by salary within each department.

32. Dense-rank employees by salary within each department.

33. Show each employee with their department's average salary.

34. Show each employee with the number of employees in their department.

35. Find the highest-paid employee in each department.

36. Find the top 2 highest-paid employees in each department.

37. Find the third-highest salary in each department.

## LAG / LEAD

38. Show every order with the previous order's amount.

39. Show every order with the next order's amount.

40. Calculate the difference between an order and the previous order.

41. Find orders whose amount is greater than the previous order.

42. For each customer, show their previous order amount.

## Running Calculations

43. Calculate the running total of order revenue.

44. Calculate running revenue separately for each customer.

45. Calculate running order count.

46. Calculate running average order value.

---

# 54. FINAL PHASE 5 CHECKLIST

Before moving to Phase 6, you should be able to explain and write:

```text
✓ Scalar subquery
✓ Multi-row subquery
✓ Subquery in WHERE
✓ Subquery in SELECT
✓ Subquery in FROM
✓ IN
✓ NOT IN
✓ EXISTS
✓ NOT EXISTS
✓ Correlated subquery
✓ ANY
✓ ALL

✓ CTE
✓ Multiple CTEs
✓ CTE + JOIN
✓ CTE + aggregation
✓ Recursive CTE
✓ Hierarchical queries

✓ Window functions
✓ OVER()
✓ PARTITION BY
✓ ROW_NUMBER()
✓ RANK()
✓ DENSE_RANK()
✓ LAG()
✓ LEAD()
✓ Window aggregates
✓ Running total
✓ Running average
✓ Top N per group
```

## FINAL MENTAL MODEL

```text
PHASE 2
Filtering
    ↓
WHERE

PHASE 3
Combining tables
    ↓
JOIN

PHASE 4
Summarizing groups
    ↓
GROUP BY
COUNT / SUM / AVG / MIN / MAX
HAVING

PHASE 5
Advanced analysis
    ↓
SUBQUERY
CTE
WINDOW FUNCTIONS
```

The most important three ideas to remember:

```text
SUBQUERY
→ query inside a query

CTE
→ named intermediate result

WINDOW FUNCTION
→ calculate across rows
  without removing the individual rows
```