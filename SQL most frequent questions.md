
For a **Senior Software Engineer (5–10 years experience)**, SQL interviews usually focus on:

- Query optimization
    
- Joins
    
- Indexing
    
- Transactions
    
- Window Functions
    
- Database Design
    
- PostgreSQL internals
    
- Concurrency
    
- Partitioning
    
- Performance tuning
    

---

# 1. Difference Between WHERE and HAVING

### Answer

`WHERE` filters rows before aggregation.

`HAVING` filters data after aggregation.

### Example

```sql
SELECT department,
       COUNT(*)
FROM employee
WHERE salary > 50000
GROUP BY department
HAVING COUNT(*) > 5;
```

Execution:

```text
FROM
WHERE
GROUP BY
HAVING
SELECT
ORDER BY
```

---

# 2. Difference Between INNER JOIN, LEFT JOIN, RIGHT JOIN, FULL JOIN

## Sample Tables

Employee

|id|name|dept_id|
|---|---|---|
|1|John|1|
|2|Sam|2|
|3|Mike|NULL|

Department

|id|dept|
|---|---|
|1|IT|
|2|HR|
|4|Finance|

---

### INNER JOIN

Returns matching records only.

```sql
SELECT *
FROM employee e
INNER JOIN department d
ON e.dept_id = d.id;
```

Result:

```text
John IT
Sam HR
```

---

### LEFT JOIN

Returns all rows from left table.

```sql
SELECT *
FROM employee e
LEFT JOIN department d
ON e.dept_id = d.id;
```

Result:

```text
John IT
Sam HR
Mike NULL
```

---

### RIGHT JOIN

Returns all rows from right table.

```sql
SELECT *
FROM employee e
RIGHT JOIN department d
ON e.dept_id = d.id;
```

Result:

```text
John IT
Sam HR
NULL Finance
```

---

### FULL JOIN

Returns all rows from both tables.

```sql
SELECT *
FROM employee e
FULL JOIN department d
ON e.dept_id = d.id;
```

---

# 3. Difference Between DELETE, TRUNCATE and DROP

### DELETE

```sql
DELETE FROM employee;
```

- DML
    
- Can rollback
    
- Row-by-row deletion
    
- Fires triggers
    

---

### TRUNCATE

```sql
TRUNCATE TABLE employee;
```

- DDL
    
- Faster
    
- Removes all rows
    
- Resets storage
    

---

### DROP

```sql
DROP TABLE employee;
```

- Removes table structure
    
- Removes data
    
- Removes indexes
    

---

# 4. What is a Primary Key?

### Answer

Uniquely identifies each row.

```sql
CREATE TABLE employee(
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100)
);
```

Properties:

- Unique
    
- Not Null
    
- One PK per table
    

---

# 5. Unique Key vs Primary Key

|Feature|Primary Key|Unique Key|
|---|---|---|
|NULL Allowed|No|Yes|
|Count|One|Multiple|
|Unique|Yes|Yes|

Example

```sql
CREATE TABLE users(
    id BIGSERIAL PRIMARY KEY,
    email VARCHAR(100) UNIQUE
);
```

---

# 6. What is a Foreign Key?

```sql
CREATE TABLE department(
    id BIGSERIAL PRIMARY KEY
);

CREATE TABLE employee(
    id BIGSERIAL PRIMARY KEY,
    dept_id BIGINT,
    CONSTRAINT fk_dept
    FOREIGN KEY(dept_id)
    REFERENCES department(id)
);
```

Ensures referential integrity.

---

# 7. Explain ON DELETE CASCADE

```sql
FOREIGN KEY(dept_id)
REFERENCES department(id)
ON DELETE CASCADE
```

When department deleted:

```text
Department removed
→ Employee rows automatically removed
```

---

# 8. What is an Index?

### Answer

Data structure that speeds up lookups.

```sql
CREATE INDEX idx_emp_name
ON employee(name);
```

Without Index

```text
O(N)
```

With Index

```text
O(log N)
```

(B-Tree)

---

# 9. Types of Indexes in PostgreSQL

### BTree

Default

```sql
CREATE INDEX idx_name
ON employee(name);
```

---

### Hash

```sql
CREATE INDEX idx_hash
ON employee USING HASH(name);
```

Equality searches.

---

### GIN

JSONB, Arrays

```sql
CREATE INDEX idx_json
ON orders USING GIN(metadata);
```

---

### GiST

Geospatial

```sql
CREATE INDEX idx_geo
ON locations USING GIST(coordinates);
```

---

### BRIN

Huge tables

```sql
CREATE INDEX idx_brin
ON logs USING BRIN(created_at);
```

---

# 10. Clustered Index vs Non Clustered Index

### PostgreSQL

Unlike SQL Server:

```text
No true clustered index
```

Can physically reorder:

```sql
CLUSTER employee USING idx_emp_name;
```

---

# 11. What is a Composite Index?

```sql
CREATE INDEX idx_emp
ON employee(department_id, salary);
```

Useful:

```sql
WHERE department_id=10
```

or

```sql
WHERE department_id=10
AND salary>50000
```

Not useful:

```sql
WHERE salary>50000
```

Leftmost prefix rule.

---

# 12. Explain ACID Properties

### Atomicity

All or nothing.

### Consistency

Valid state → valid state.

### Isolation

Transactions don't interfere.

### Durability

Committed data survives crash.

---

# 13. What is a Transaction?

```sql
BEGIN;

UPDATE account
SET balance=balance-1000
WHERE id=1;

UPDATE account
SET balance=balance+1000
WHERE id=2;

COMMIT;
```

---

# 14. What is Rollback?

```sql
BEGIN;

UPDATE employee
SET salary=999999;

ROLLBACK;
```

Changes reverted.

---

# 15. Isolation Levels

---

### Read Uncommitted

Postgres treats as Read Committed.

---

### Read Committed (Default)

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

No dirty reads.

---

### Repeatable Read

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

Consistent snapshot.

---

### Serializable

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

Highest isolation.

---

# 16. What is a Deadlock?

Transaction A:

```sql
UPDATE account SET balance=0 WHERE id=1;
```

Transaction B:

```sql
UPDATE account SET balance=0 WHERE id=2;
```

Then each tries to update the row locked by the other.

Result:

```text
Deadlock detected
```

Postgres kills one transaction.

---

# 17. Explain Window Functions

### Rank Employees

```sql
SELECT
    name,
    salary,
    RANK() OVER(
        ORDER BY salary DESC
    ) rank
FROM employee;
```

---

# 18. ROW_NUMBER vs RANK vs DENSE_RANK

Data

```text
100
100
90
```

ROW_NUMBER

```text
1
2
3
```

RANK

```text
1
1
3
```

DENSE_RANK

```text
1
1
2
```

---

# 19. Find Second Highest Salary

### Window Function

```sql
SELECT salary
FROM (
    SELECT salary,
           DENSE_RANK() OVER(
               ORDER BY salary DESC
           ) rnk
    FROM employee
) x
WHERE rnk = 2;
```

---

# 20. Find Duplicate Records

```sql
SELECT email,
       COUNT(*)
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

---

# 21. Delete Duplicate Records

```sql
DELETE FROM users
WHERE id IN (
    SELECT id
    FROM (
        SELECT id,
               ROW_NUMBER() OVER(
                   PARTITION BY email
                   ORDER BY id
               ) rn
        FROM users
    ) t
    WHERE rn > 1
);
```

---

# 22. What is CTE?

```sql
WITH high_salary AS (
    SELECT *
    FROM employee
    WHERE salary > 100000
)
SELECT *
FROM high_salary;
```

Improves readability.

---

# 23. Recursive CTE Example

```sql
WITH RECURSIVE emp_tree AS (
    SELECT id,name,manager_id
    FROM employee
    WHERE manager_id IS NULL

    UNION ALL

    SELECT e.id,e.name,e.manager_id
    FROM employee e
    JOIN emp_tree t
      ON e.manager_id=t.id
)
SELECT *
FROM emp_tree;
```

---

# 24. What is EXPLAIN?

```sql
EXPLAIN
SELECT *
FROM employee
WHERE id=100;
```

Shows execution plan.

---

# 25. What is EXPLAIN ANALYZE?

```sql
EXPLAIN ANALYZE
SELECT *
FROM employee
WHERE id=100;
```

Actually executes query and shows:

- Actual rows
    
- Actual time
    
- Cost
    

---

# 26. Why Query is Slow?

Common reasons:

- Missing index
    
- Too many joins
    
- Full table scan
    
- Wrong datatype
    
- Functions in WHERE clause
    
- Large sort operations
    

---

# 27. What is VACUUM?

Postgres uses MVCC.

Deleted rows remain temporarily.

```sql
VACUUM employee;
```

Reclaims dead tuples.

---

# 28. What is AUTOVACUUM?

Background process that automatically:

- Vacuum
    
- Analyze
    

Prevents table bloat.

---

# 29. What is ANALYZE?

Updates statistics.

```sql
ANALYZE employee;
```

Planner uses statistics to choose best plan.

---

# 30. What is MVCC?

Multi Version Concurrency Control.

Benefits:

- Readers don't block writers.
    
- Writers don't block readers.
    

Core reason PostgreSQL scales well under concurrent workloads.

---

# 31. Partitioning in PostgreSQL

```sql
CREATE TABLE orders (
    id BIGINT,
    created_at DATE
)
PARTITION BY RANGE(created_at);
```

Partition:

```sql
CREATE TABLE orders_2026
PARTITION OF orders
FOR VALUES FROM ('2026-01-01')
TO ('2027-01-01');
```

Benefits:

- Faster queries
    
- Easier maintenance
    

---

# 32. Difference Between UNION and UNION ALL

### UNION

Removes duplicates.

```sql
SELECT name FROM employee
UNION
SELECT name FROM customer;
```

---

### UNION ALL

Keeps duplicates.

```sql
SELECT name FROM employee
UNION ALL
SELECT name FROM customer;
```

Faster.

---

# 33. EXISTS vs IN

### EXISTS

```sql
SELECT *
FROM employee e
WHERE EXISTS (
    SELECT 1
    FROM department d
    WHERE d.id=e.dept_id
);
```

Best for large subqueries.

---

### IN

```sql
SELECT *
FROM employee
WHERE dept_id IN (
    SELECT id
    FROM department
);
```

Good for smaller sets.

---

# 34. PostgreSQL JSONB Interview Question

Store:

```sql
CREATE TABLE orders(
    id BIGSERIAL,
    details JSONB
);
```

Query:

```sql
SELECT *
FROM orders
WHERE details->>'status'='SUCCESS';
```

Index:

```sql
CREATE INDEX idx_json
ON orders
USING GIN(details);
```

---

# 35. Most Important Senior-Level SQL Performance Question

### Query

```sql
SELECT *
FROM orders
WHERE customer_id=10
AND created_at >= CURRENT_DATE - 30;
```

Best Index:

```sql
CREATE INDEX idx_orders_customer_date
ON orders(customer_id, created_at);
```

Reason:

```text
customer_id = equality
created_at = range
```

Equality columns should usually come first, then range columns.

---

These 35 questions cover roughly **80–90% of SQL questions asked in senior Java/backend interviews at companies like JP Morgan, Goldman Sachs, Morgan Stanley, Walmart, Adobe, Atlassian, Publicis Sapient, and product companies**, especially when PostgreSQL is the primary database.


---

# Problems

For **Senior Engineer SQL interviews**, query-writing questions are much more common than theory. Interviewers typically give a table schema and ask you to write a query.

Below are some of the most frequently asked problem-solving SQL questions with PostgreSQL solutions.

---

# 1. Find Second Highest Salary

### Table

```sql
Employee
+----+--------+
| id | salary |
+----+--------+
| 1  | 10000  |
| 2  | 20000  |
| 3  | 30000  |
| 4  | 30000  |
+----+--------+
```

### Query

```sql
SELECT DISTINCT salary
FROM employee
ORDER BY salary DESC
OFFSET 1
LIMIT 1;
```

---

# 2. Find Nth Highest Salary

### N = 3

```sql
SELECT DISTINCT salary
FROM employee
ORDER BY salary DESC
OFFSET 2
LIMIT 1;
```

---

# 3. Find Employees Having Highest Salary In Each Department

### Tables

```sql
employee(id,name,salary,dept_id)
```

### Query

```sql
SELECT *
FROM (
    SELECT e.*,
           DENSE_RANK() OVER(
               PARTITION BY dept_id
               ORDER BY salary DESC
           ) rnk
    FROM employee e
) t
WHERE rnk = 1;
```

---

# 4. Find Duplicate Emails

```sql
SELECT email,
       COUNT(*)
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

---

# 5. Delete Duplicate Records

Keep lowest id.

```sql
DELETE FROM users
WHERE id IN (
    SELECT id
    FROM (
        SELECT id,
               ROW_NUMBER() OVER(
                   PARTITION BY email
                   ORDER BY id
               ) rn
        FROM users
    ) x
    WHERE rn > 1
);
```

---

# 6. Find Employees Who Earn More Than Their Manager

### Table

```sql
employee
(
 id,
 name,
 salary,
 manager_id
)
```

### Query

```sql
SELECT e.name
FROM employee e
JOIN employee m
ON e.manager_id = m.id
WHERE e.salary > m.salary;
```

---

# 7. Find Customers Who Never Ordered

### Tables

```sql
customers(id,name)

orders(id,customer_id)
```

### Query

```sql
SELECT c.*
FROM customers c
LEFT JOIN orders o
ON c.id = o.customer_id
WHERE o.customer_id IS NULL;
```

---

# 8. Find Customers Who Ordered More Than Once

```sql
SELECT customer_id,
       COUNT(*)
FROM orders
GROUP BY customer_id
HAVING COUNT(*) > 1;
```

---

# 9. Find Top 3 Highest Salaries

```sql
SELECT DISTINCT salary
FROM employee
ORDER BY salary DESC
LIMIT 3;
```

---

# 10. Find Top 3 Employees Per Department

```sql
SELECT *
FROM (
    SELECT e.*,
           ROW_NUMBER() OVER(
               PARTITION BY dept_id
               ORDER BY salary DESC
           ) rn
    FROM employee e
) t
WHERE rn <= 3;
```

---

# 11. Find Consecutive Login Days

### Table

```sql
logins(user_id, login_date)
```

### Query

```sql
WITH cte AS (
    SELECT *,
           login_date -
           ROW_NUMBER() OVER(
               PARTITION BY user_id
               ORDER BY login_date
           )::int grp
    FROM logins
)
SELECT user_id,
       MIN(login_date),
       MAX(login_date),
       COUNT(*)
FROM cte
GROUP BY user_id, grp;
```

---

# 12. Find Running Total

```sql
SELECT
    id,
    amount,
    SUM(amount)
    OVER(
        ORDER BY id
    ) running_total
FROM transactions;
```

---

# 13. Find Moving Average

```sql
SELECT
    id,
    amount,
    AVG(amount)
    OVER(
        ORDER BY id
        ROWS BETWEEN 2 PRECEDING
        AND CURRENT ROW
    ) avg_amount
FROM transactions;
```

---

# 14. Find Month Over Month Growth

```sql
SELECT
    month,
    revenue,
    revenue -
    LAG(revenue)
    OVER(ORDER BY month)
    growth
FROM sales;
```

---

# 15. Find Percentage Contribution

```sql
SELECT
    department,
    salary,
    ROUND(
        salary * 100.0 /
        SUM(salary) OVER(),
        2
    ) percentage
FROM employee;
```

---

# 16. Find First Order For Every Customer

```sql
SELECT *
FROM (
    SELECT *,
           ROW_NUMBER() OVER(
               PARTITION BY customer_id
               ORDER BY order_date
           ) rn
    FROM orders
) t
WHERE rn = 1;
```

---

# 17. Find Latest Order Per Customer

```sql
SELECT *
FROM (
    SELECT *,
           ROW_NUMBER() OVER(
               PARTITION BY customer_id
               ORDER BY order_date DESC
           ) rn
    FROM orders
) t
WHERE rn = 1;
```

---

# 18. Find Missing Numbers

### Table

```sql
numbers
-------
1
2
4
5
7
```

### Query

```sql
SELECT n
FROM generate_series(1,7) n
LEFT JOIN numbers t
ON n = t.number
WHERE t.number IS NULL;
```

---

# 19. Find Gaps Between Dates

```sql
SELECT
    order_date,
    LEAD(order_date)
    OVER(ORDER BY order_date)
    - order_date AS gap
FROM orders;
```

---

# 20. Find Department With Highest Average Salary

```sql
SELECT dept_id,
       AVG(salary)
FROM employee
GROUP BY dept_id
ORDER BY AVG(salary) DESC
LIMIT 1;
```

---

# 21. Find Employees Joined In Last 30 Days

```sql
SELECT *
FROM employee
WHERE joining_date >= CURRENT_DATE - INTERVAL '30 days';
```

---

# 22. Find Employees Having Same Salary

```sql
SELECT salary
FROM employee
GROUP BY salary
HAVING COUNT(*) > 1;
```

---

# 23. Find Product Sold Most

```sql
SELECT product_id,
       COUNT(*)
FROM orders
GROUP BY product_id
ORDER BY COUNT(*) DESC
LIMIT 1;
```

---

# 24. Find Revenue Per Month

```sql
SELECT
    DATE_TRUNC('month', order_date),
    SUM(amount)
FROM orders
GROUP BY DATE_TRUNC('month', order_date);
```

---

# 25. Find Users Active Every Day

### Table

```sql
activity(user_id, activity_date)
```

### Query

```sql
SELECT user_id
FROM activity
GROUP BY user_id
HAVING COUNT(DISTINCT activity_date) =
(
    SELECT COUNT(DISTINCT activity_date)
    FROM activity
);
```

---

# 26. Find Median Salary

```sql
SELECT
PERCENTILE_CONT(0.5)
WITHIN GROUP
(ORDER BY salary)
FROM employee;
```

---

# 27. Find Employees Not Assigned To Department

```sql
SELECT *
FROM employee
WHERE dept_id IS NULL;
```

---

# 28. Find Common Records Between Two Tables

```sql
SELECT *
FROM table1
INTERSECT
SELECT *
FROM table2;
```

---

# 29. Find Records Present In One Table But Not Another

```sql
SELECT *
FROM table1
EXCEPT
SELECT *
FROM table2;
```

---

# 30. Find Highest Salary Without LIMIT

```sql
SELECT MAX(salary)
FROM employee;
```

Second highest:

```sql
SELECT MAX(salary)
FROM employee
WHERE salary <
(
    SELECT MAX(salary)
    FROM employee
);
```

---

# 31. Find Continuous Streak Of Purchases

```sql
WITH cte AS (
    SELECT *,
           purchase_date -
           ROW_NUMBER()
           OVER(
               PARTITION BY customer_id
               ORDER BY purchase_date
           )::int grp
    FROM purchases
)
SELECT customer_id,
       COUNT(*)
FROM cte
GROUP BY customer_id, grp;
```

---

# 32. Find Employee Count Hierarchy

```sql
WITH RECURSIVE emp_tree AS (
    SELECT id,
           manager_id
    FROM employee

    UNION ALL

    SELECT e.id,
           e.manager_id
    FROM employee e
    JOIN emp_tree t
    ON e.manager_id=t.id
)
SELECT *
FROM emp_tree;
```

---

# 33. Find Manager With Most Direct Reports

```sql
SELECT manager_id,
       COUNT(*)
FROM employee
GROUP BY manager_id
ORDER BY COUNT(*) DESC
LIMIT 1;
```

---

# 34. Find Latest Record Per Group

Very common interview question.

```sql
SELECT *
FROM (
    SELECT *,
           ROW_NUMBER() OVER(
               PARTITION BY customer_id
               ORDER BY created_at DESC
           ) rn
    FROM orders
) t
WHERE rn = 1;
```

---

# 35. Sessionization Problem (FAANG Favorite)

Create user sessions if gap > 30 mins.

```sql
WITH cte AS (
    SELECT *,
           CASE
             WHEN event_time -
                  LAG(event_time)
                  OVER(
                     PARTITION BY user_id
                     ORDER BY event_time
                  )
                  > INTERVAL '30 minutes'
             THEN 1
             ELSE 0
           END flag
    FROM events
)
SELECT *
FROM cte;
```

---

These 35 questions are asked repeatedly in senior backend interviews because they test:

- Joins
    
- Aggregations
    
- Window Functions
    
- CTEs
    
- Recursive Queries
    
- Ranking
    
- Running Totals
    
- Gaps & Islands
    
- Sessionization
    
- Time Series Analytics
    
- Reporting Queries
    
- Real-world business use cases
    

If you're targeting companies like JP Morgan, Goldman Sachs, Walmart, Atlassian, Adobe, Uber, Swiggy, Flipkart, or senior Java backend roles, I'd add another set of **25 advanced PostgreSQL SQL challenges** focused on **window functions, recursive CTEs, and query optimization**, because those are often the differentiators at the senior level.


---
I have 3 table where author_book_id table will have the forign key from the author and book table.

How can I list all the books written by specific author

Answer:

Assume you have these tables:

```sql
CREATE TABLE author (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE book (
    id BIGSERIAL PRIMARY KEY,
    title VARCHAR(200)
);

CREATE TABLE author_book (
    author_id BIGINT,
    book_id BIGINT,
    PRIMARY KEY (author_id, book_id),
    FOREIGN KEY (author_id) REFERENCES author(id),
    FOREIGN KEY (book_id) REFERENCES book(id)
);
```

## Sample Data

```sql
INSERT INTO author(id, name)
VALUES
(1, 'Robert Martin'),
(2, 'Joshua Bloch');

INSERT INTO book(id, title)
VALUES
(101, 'Clean Code'),
(102, 'Clean Architecture'),
(103, 'Effective Java');

INSERT INTO author_book(author_id, book_id)
VALUES
(1, 101),
(1, 102),
(2, 103);
```

---

## Find all books written by a specific author

### Using Author ID

```sql
SELECT b.*
FROM book b
INNER JOIN author_book ab
    ON b.id = ab.book_id
WHERE ab.author_id = 1;
```

**Result**

| id  | title              |
| --- | ------------------ |
| 101 | Clean Code         |
| 102 | Clean Architecture |

---

### Using Author Name

```sql
SELECT b.*
FROM book b
INNER JOIN author_book ab
    ON b.id = ab.book_id
INNER JOIN author a
    ON a.id = ab.author_id
WHERE a.name = 'Robert Martin';
```

---

## Return Author Name + Book Title

```sql
SELECT
    a.name AS author_name,
    b.title AS book_title
FROM author a
INNER JOIN author_book ab
    ON a.id = ab.author_id
INNER JOIN book b
    ON b.id = ab.book_id
WHERE a.id = 1;
```

**Result**

| author_name   | book_title         |
| ------------- | ------------------ |
| Robert Martin | Clean Code         |
| Robert Martin | Clean Architecture |

---

## Get all authors and their books

```sql
SELECT
    a.name,
    b.title
FROM author a
INNER JOIN author_book ab
    ON a.id = ab.author_id
INNER JOIN book b
    ON b.id = ab.book_id
ORDER BY a.name, b.title;
```

---

## PostgreSQL: Get all books in a single row

```sql
SELECT
    a.name,
    STRING_AGG(b.title, ', ') AS books
FROM author a
INNER JOIN author_book ab
    ON a.id = ab.author_id
INNER JOIN book b
    ON b.id = ab.book_id
WHERE a.id = 1
GROUP BY a.name;
```

**Result**

| name          | books                          |
| ------------- | ------------------------------ |
| Robert Martin | Clean Code, Clean Architecture |

This pattern (`author -> author_book -> book`) is the standard **many-to-many relationship** in relational databases. The bridge table (`author_book`) is joined to both parent tables to fetch related records.
