
# 1. Create Table

A table stores data in rows and columns.

### Syntax

```sql
CREATE TABLE employee (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255),
    salary NUMERIC(10,2),
    joining_date DATE,
    active BOOLEAN DEFAULT TRUE
);
```

### Explanation

|Column|Description|
|---|---|
|BIGSERIAL|Auto-incrementing bigint|
|PRIMARY KEY|Unique identifier|
|VARCHAR(100)|String with max length 100|
|NUMERIC(10,2)|Decimal number|
|DATE|Stores date|
|BOOLEAN|true/false|

### Verify

```sql
SELECT * FROM employee;
```

---

# 2. Insert Data

Insert single row.

```sql
INSERT INTO employee
(name, email, salary, joining_date)
VALUES
('Anoop', 'anoop@gmail.com', 50000, '2026-01-10');
```

---

### Insert Multiple Rows

```sql
INSERT INTO employee
(name, email, salary, joining_date)
VALUES
('Rahul', 'rahul@gmail.com', 60000, '2025-01-01'),
('Amit', 'amit@gmail.com', 70000, '2024-05-15'),
('Priya', 'priya@gmail.com', 80000, '2023-10-01');
```

---

### Insert from another table

```sql
INSERT INTO employee_backup
SELECT * FROM employee;
```

---

# 3. Update Data

Update existing rows.

### Update Single Row

```sql
UPDATE employee
SET salary = 90000
WHERE id = 1;
```

---

### Update Multiple Columns

```sql
UPDATE employee
SET
    salary = 95000,
    active = FALSE
WHERE id = 2;
```

---

### Update Using Expression

```sql
UPDATE employee
SET salary = salary * 1.10;
```

Increase everyone's salary by 10%.

---

### Update With Join

```sql
UPDATE employee e
SET salary = d.default_salary
FROM department d
WHERE e.department_id = d.id;
```

---

# 4. Delete Data

### Delete Specific Row

```sql
DELETE FROM employee
WHERE id = 1;
```

---

### Delete Multiple Rows

```sql
DELETE FROM employee
WHERE salary < 50000;
```

---

### Delete All Rows

```sql
DELETE FROM employee;
```

---

### Faster Delete

```sql
TRUNCATE TABLE employee;
```

Difference:

|DELETE|TRUNCATE|
|---|---|
|Row by row|Removes all rows|
|Can use WHERE|No WHERE|
|Slower|Faster|

---

# 5. Alter Table

Used to modify existing table structure.

---

## Add Column

```sql
ALTER TABLE employee
ADD COLUMN phone VARCHAR(20);
```

---

## Add Multiple Columns

```sql
ALTER TABLE employee
ADD COLUMN city VARCHAR(100),
ADD COLUMN state VARCHAR(100);
```

---

## Rename Column

```sql
ALTER TABLE employee
RENAME COLUMN phone TO mobile_number;
```

---

## Change Data Type

```sql
ALTER TABLE employee
ALTER COLUMN salary TYPE BIGINT;
```

---

## Set Default Value

```sql
ALTER TABLE employee
ALTER COLUMN active SET DEFAULT FALSE;
```

---

## Drop Column

```sql
ALTER TABLE employee
DROP COLUMN state;
```

---

## Rename Table

```sql
ALTER TABLE employee
RENAME TO employees;
```

---

# 6. Create Index

Index improves query performance.

Without index:

```sql
SELECT *
FROM employee
WHERE email = 'anoop@gmail.com';
```

Database scans entire table.

---

## Normal Index

```sql
CREATE INDEX idx_employee_email
ON employee(email);
```

---

## Composite Index

Useful when filtering by multiple columns.

```sql
CREATE INDEX idx_emp_name_salary
ON employee(name, salary);
```

Query:

```sql
SELECT *
FROM employee
WHERE name='Anoop'
AND salary > 50000;
```

---

## Unique Index

```sql
CREATE UNIQUE INDEX idx_email_unique
ON employee(email);
```

No duplicate emails allowed.

---

## Drop Index

```sql
DROP INDEX idx_employee_email;
```

---

# 7. Unique Key

Ensures uniqueness.

---

## Column Level

```sql
CREATE TABLE employee (
    id BIGSERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE
);
```

---

## Table Level

```sql
CREATE TABLE employee (
    id BIGSERIAL PRIMARY KEY,
    email VARCHAR(255),
    CONSTRAINT uk_employee_email UNIQUE(email)
);
```

---

## Composite Unique Key

Combination must be unique.

```sql
CREATE TABLE student (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100),
    class_id BIGINT,
    CONSTRAINT uk_student UNIQUE(name, class_id)
);
```

Allowed:

```text
Anoop | Class 1
Anoop | Class 2
```

Not allowed:

```text
Anoop | Class 1
Anoop | Class 1
```

---

# 8. Foreign Key

Foreign key maintains parent-child relationship.

---

## Parent Table

```sql
CREATE TABLE department (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100)
);
```

---

## Child Table

```sql
CREATE TABLE employee (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100),
    department_id BIGINT,

    CONSTRAINT fk_employee_department
    FOREIGN KEY (department_id)
    REFERENCES department(id)
);
```

---

# Foreign Key Cascade Options

Postgres supports:

## 1. CASCADE

If parent row deleted/updated, child rows automatically deleted/updated.

```sql
FOREIGN KEY (department_id)
REFERENCES department(id)
ON DELETE CASCADE
ON UPDATE CASCADE
```

### Example

Department:

```text
10 IT
```

Employee:

```text
1 Anoop 10
2 Rahul 10
```

Delete department:

```sql
DELETE FROM department
WHERE id = 10;
```

Result:

```text
department -> deleted
employee -> deleted automatically
```

---

## 2. RESTRICT

Prevent delete/update if children exist.

```sql
FOREIGN KEY (department_id)
REFERENCES department(id)
ON DELETE RESTRICT
```

Example:

```sql
DELETE FROM department
WHERE id = 10;
```

Error:

```text
Cannot delete because employees exist
```

---

## 3. NO ACTION

Default behavior.

```sql
FOREIGN KEY (department_id)
REFERENCES department(id)
ON DELETE NO ACTION
```

Very similar to RESTRICT.

Difference:

- RESTRICT checks immediately.
    
- NO ACTION checks after statement completion.
    

---

## 4. SET NULL

Child FK becomes NULL.

```sql
FOREIGN KEY (department_id)
REFERENCES department(id)
ON DELETE SET NULL
```

Before:

```text
Employee
--------
Anoop -> 10
Rahul -> 10
```

Delete department:

```sql
DELETE FROM department
WHERE id = 10;
```

After:

```text
Anoop -> NULL
Rahul -> NULL
```

---

## 5. SET DEFAULT

Set FK to default value.

```sql
department_id BIGINT DEFAULT 1,

FOREIGN KEY (department_id)
REFERENCES department(id)
ON DELETE SET DEFAULT
```

Before:

```text
Anoop -> 10
```

Delete department 10:

```text
Anoop -> 1
```

---

# Complete Example

```sql
CREATE TABLE department (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) UNIQUE
);

CREATE TABLE employee (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE,
    salary NUMERIC(10,2),

    department_id BIGINT,

    CONSTRAINT fk_employee_department
    FOREIGN KEY (department_id)
    REFERENCES department(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);
```

# Most Common Constraints in Real Projects

```sql
PRIMARY KEY
UNIQUE
FOREIGN KEY
NOT NULL
CHECK
DEFAULT
INDEX
```

Example:

```sql
CREATE TABLE employee (
    id BIGSERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    age INT CHECK(age >= 18),
    active BOOLEAN DEFAULT TRUE
);
```

These cover about 90% of the DDL (schema) operations you will use in PostgreSQL-backed Java applications such as Spring Boot, Hibernate, and microservices.