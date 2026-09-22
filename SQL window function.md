
Window Functions are one of the most powerful features in PostgreSQL. They allow you to perform calculations across a set of rows related to the current row **without collapsing rows like GROUP BY does**.

## Sample Table

We'll use this table throughout:

```sql
CREATE TABLE employee (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(50),
    salary NUMERIC,
    joining_date DATE
);

INSERT INTO employee(name, department, salary, joining_date)
VALUES
('John', 'IT', 80000, '2020-01-01'),
('Alice', 'IT', 90000, '2021-01-01'),
('Bob', 'IT', 85000, '2022-01-01'),
('Tom', 'HR', 50000, '2019-01-01'),
('Jerry', 'HR', 60000, '2020-01-01'),
('David', 'Finance', 70000, '2018-01-01');
```

---

# Window Function Syntax

```sql
window_function() OVER (
    PARTITION BY ...
    ORDER BY ...
)
```

- `PARTITION BY` → Similar to GROUP BY but doesn't collapse rows.
    
- `ORDER BY` → Defines ordering within partition.
    

---

# 1. ROW_NUMBER()

Assigns unique sequence numbers.

## Use Case

- Pagination
    
- Remove duplicates
    
- Find latest record
    

```sql
SELECT
    name,
    department,
    salary,
    ROW_NUMBER() OVER(
        PARTITION BY department
        ORDER BY salary DESC
    ) AS rn
FROM employee;
```

Output:

|Name|Dept|Salary|RN|
|---|---|---|---|
|Alice|IT|90000|1|
|Bob|IT|85000|2|
|John|IT|80000|3|
|Jerry|HR|60000|1|
|Tom|HR|50000|2|

---

# 2. RANK()

Same rank for ties, but skips numbers.

```sql
SELECT
    name,
    salary,
    RANK() OVER(ORDER BY salary DESC) rank
FROM employee;
```

Example:

|Salary|Rank|
|---|---|
|90000|1|
|85000|2|
|85000|2|
|80000|4|

Notice rank 3 skipped.

---

# 3. DENSE_RANK()

No gaps.

```sql
SELECT
    name,
    salary,
    DENSE_RANK() OVER(ORDER BY salary DESC)
FROM employee;
```

Output:

|Salary|Dense Rank|
|---|---|
|90000|1|
|85000|2|
|85000|2|
|80000|3|

---

# 4. NTILE()

Split rows into buckets.

## Use Case

- Quartiles
    
- Top 25%
    
- Performance grading
    

```sql
SELECT
    name,
    salary,
    NTILE(4) OVER(ORDER BY salary DESC) quartile
FROM employee;
```

Output:

```text
Quartile 1 = Top 25%
Quartile 4 = Bottom 25%
```

---

# 5. LEAD()

Access next row.

## Use Case

- Compare current vs next day
    
- Stock prices
    
- Trend analysis
    

```sql
SELECT
    name,
    salary,
    LEAD(salary) OVER(
        ORDER BY joining_date
    ) next_salary
FROM employee;
```

Output:

|Name|Salary|Next Salary|
|---|---|---|
|David|70000|50000|
|Tom|50000|80000|

---

# 6. LAG()

Access previous row.

## Use Case

- Previous transaction
    
- Previous stock price
    
- Previous month sales
    

```sql
SELECT
    name,
    salary,
    LAG(salary) OVER(
        ORDER BY joining_date
    ) prev_salary
FROM employee;
```

---

# 7. FIRST_VALUE()

First row in partition.

## Use Case

Find highest salary employee per department.

```sql
SELECT
    name,
    department,
    salary,
    FIRST_VALUE(name) OVER(
        PARTITION BY department
        ORDER BY salary DESC
    ) top_employee
FROM employee;
```

---

# 8. LAST_VALUE()

Last row in partition.

```sql
SELECT
    name,
    department,
    salary,
    LAST_VALUE(name) OVER(
        PARTITION BY department
        ORDER BY salary DESC
        ROWS BETWEEN UNBOUNDED PRECEDING
        AND UNBOUNDED FOLLOWING
    ) lowest_paid
FROM employee;
```

⚠️ Without the explicit frame clause, LAST_VALUE often gives unexpected results.

---

# 9. NTH_VALUE()

Get nth row.

```sql
SELECT
    name,
    department,
    salary,
    NTH_VALUE(name, 2)
    OVER(
        PARTITION BY department
        ORDER BY salary DESC
        ROWS BETWEEN UNBOUNDED PRECEDING
        AND UNBOUNDED FOLLOWING
    )
FROM employee;
```

Get second highest paid employee.

---

# 10. Running Total (SUM)

One of the most common interview questions.

```sql
SELECT
    joining_date,
    salary,
    SUM(salary)
    OVER(
        ORDER BY joining_date
    ) running_total
FROM employee;
```

Output:

|Salary|Running Total|
|---|---|
|70000|70000|
|50000|120000|
|80000|200000|

---

# 11. Moving Average

## Use Case

- Stock analysis
    
- Sales forecasting
    

```sql
SELECT
    joining_date,
    salary,
    AVG(salary)
    OVER(
        ORDER BY joining_date
        ROWS BETWEEN 2 PRECEDING
        AND CURRENT ROW
    ) moving_avg
FROM employee;
```

Calculates 3-row moving average.

---

# 12. Cumulative Average

```sql
SELECT
    salary,
    AVG(salary)
    OVER(
        ORDER BY joining_date
    ) cumulative_avg
FROM employee;
```

---

# 13. Partitioned SUM

Equivalent of GROUP BY while keeping all rows.

```sql
SELECT
    name,
    department,
    salary,
    SUM(salary)
    OVER(
        PARTITION BY department
    ) dept_total_salary
FROM employee;
```

Output:

|Name|Dept|Dept Total|
|---|---|---|
|John|IT|255000|
|Alice|IT|255000|
|Bob|IT|255000|

---

# 14. Department Percentage Contribution

```sql
SELECT
    name,
    salary,
    ROUND(
        salary * 100.0 /
        SUM(salary) OVER(PARTITION BY department),
        2
    ) contribution_pct
FROM employee;
```

Output:

```text
Alice = 35.29%
Bob = 33.33%
John = 31.37%
```

---

# 15. Difference From Previous Row

```sql
SELECT
    joining_date,
    salary,
    salary -
    LAG(salary)
    OVER(ORDER BY joining_date)
    AS diff
FROM employee;
```

Used in:

- Daily stock movement
    
- Revenue changes
    
- Temperature changes
    

---

# 16. Top N Per Group

Very common interview question.

```sql
WITH ranked AS (
    SELECT *,
           ROW_NUMBER() OVER(
               PARTITION BY department
               ORDER BY salary DESC
           ) rn
    FROM employee
)
SELECT *
FROM ranked
WHERE rn <= 2;
```

Get top 2 salaries per department.

---

# 17. Duplicate Detection

```sql
WITH duplicates AS (
    SELECT *,
           ROW_NUMBER() OVER(
               PARTITION BY name
               ORDER BY id
           ) rn
    FROM employee
)
SELECT *
FROM duplicates
WHERE rn > 1;
```

---

# 18. Gap Analysis

Find missing sequence values.

```sql
CREATE TABLE orders (
    order_id INT
);

SELECT
    order_id,
    LEAD(order_id)
        OVER(ORDER BY order_id)
        AS next_id
FROM orders;
```

Find:

```sql
WHERE next_id - order_id > 1
```

Useful for invoice numbers.

---

# 19. Percentile Ranking

```sql
SELECT
    name,
    salary,
    PERCENT_RANK()
    OVER(ORDER BY salary)
FROM employee;
```

Output range:

```text
0.0 → Lowest
1.0 → Highest
```

---

# 20. Relative Position (CUME_DIST)

```sql
SELECT
    name,
    salary,
    CUME_DIST()
    OVER(ORDER BY salary)
FROM employee;
```

Use case:

```text
Top 10%
Top 20%
Top 50%
```

---

# 21. Window COUNT

Count rows without grouping.

```sql
SELECT
    name,
    department,
    COUNT(*)
    OVER(PARTITION BY department)
    AS employee_count
FROM employee;
```

---

# 22. Window MIN/MAX

```sql
SELECT
    name,
    department,
    salary,
    MAX(salary)
    OVER(PARTITION BY department)
    AS max_salary
FROM employee;
```

---

# 23. Sessionization (Advanced)

Find user sessions separated by inactivity.

```sql
WITH user_events AS (
    SELECT
        user_id,
        event_time,
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
        END AS new_session
    FROM events
)
SELECT * FROM user_events;
```

Used in:

- Banking
    
- E-commerce
    
- Analytics
    

---

# Most Common Real-World Uses in Banking/FinTech

Since you're working in banking systems, these are the window functions you'll use most:

|Use Case|Function|
|---|---|
|Top N customers|ROW_NUMBER|
|Transaction ranking|RANK|
|Previous transaction|LAG|
|Next payment due|LEAD|
|Running balance|SUM OVER|
|Moving average spending|AVG OVER|
|Fraud detection patterns|LAG + LEAD|
|Customer percentile|PERCENT_RANK|
|Department totals|SUM OVER(PARTITION BY)|
|Session analysis|LAG|

### Top 10 Window Functions Asked in Interviews

1. ROW_NUMBER()
    
2. RANK()
    
3. DENSE_RANK()
    
4. LAG()
    
5. LEAD()
    
6. SUM() OVER()
    
7. AVG() OVER()
    
8. FIRST_VALUE()
    
9. NTILE()
    
10. PERCENT_RANK()
    

If you're preparing for Java backend interviews (JP Morgan, Goldman Sachs, Morgan Stanley, Walmart, Amazon, Flipkart), mastering these 10 functions plus window frames (`ROWS BETWEEN`) will cover the vast majority of SQL window-function questions.