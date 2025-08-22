# Basic SQL
## 1. SQL Syntax
```sql
SELECT column1, column2
FROM table_name;

SELECT * 
FROM table_name;

SELECT DISTINCT column1
FROM table_name;

SELECT column1, column2
FROM table_name
WHERE condition;

SELECT column1, column2
FROM table_name
ORDER BY column1 ASC, column2 DESC;

SELECT column1
FROM table_name
LIMIT 10;          -- MySQL / PostgreSQL / SQLite

SELECT TOP 10 *
FROM table_name;   -- SQL Server
```
## 2. Filtering Data
```sql
-- Comparison
=   <>   !=   >   >=   <   <=

-- Logic
AND   OR   NOT

-- Ranges
column BETWEEN value1 AND value2

-- Pattern matching
column LIKE 'A%'     
column LIKE '%A'     
column LIKE '%A%'    
column NOT LIKE '%A%'

-- Null values
column IS NULL
column IS NOT NULL

-- Sets
column IN ('A', 'B', 'C')
column NOT IN ('A', 'B', 'C')
```
## 3. Aggregate Functions
```sql
SELECT
    COUNT(*) AS total_rows,
    SUM(column) AS total_sum,
    AVG(column) AS average,
    MIN(column) AS minimum,
    MAX(column) AS maximum
FROM table_name;
```
## 4. Grouping Data
```sql
SELECT department, COUNT(*) AS employee_count
FROM employees
GROUP BY department;

SELECT department, COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING COUNT(*) > 10;
```
## 5. Joins
```sql
-- Inner Join
SELECT a.id, a.name, b.order_id
FROM customers a
INNER JOIN orders b
ON a.id = b.customer_id;

-- Left Join
SELECT a.id, a.name, b.order_id
FROM customers a
LEFT JOIN orders b
ON a.id = b.customer_id;

-- Right Join
SELECT a.id, a.name, b.order_id
FROM customers a
RIGHT JOIN orders b
ON a.id = b.customer_id;

-- Full Outer Join
SELECT a.id, a.name, b.order_id
FROM customers a
FULL OUTER JOIN orders b
ON a.id = b.customer_id;

-- Cross Join
SELECT a.name, b.product
FROM customers a
CROSS JOIN products b;
```
## 6. Subqueries
```sql
SELECT name
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);

SELECT department, avg_salary
FROM (
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
) AS dept_avg;
```
## 7. Common Table Expressions (CTE)
```sql
WITH dept_avg AS (
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
)
SELECT e.name, e.salary, d.avg_salary
FROM employees e
JOIN dept_avg d
ON e.department = d.department;
```
## 8. Windows Functions
```sql
SELECT name, salary,
       RANK() OVER (ORDER BY salary DESC) AS rank_num,
       ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num,
       DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rank
FROM employees;

SELECT name, salary,
       SUM(salary) OVER (ORDER BY name) AS running_total
FROM employees;

SELECT department, name, salary,
       AVG(salary) OVER (PARTITION BY department) AS dept_avg
FROM employees;
```
## 9. Data Modification
```sql
INSERT INTO employees (name, department, salary)
VALUES ('Alice', 'IT', 5000);

INSERT INTO employees (name, department, salary)
VALUES
('Bob', 'HR', 4000),
('Charlie', 'IT', 6000);

UPDATE employees
SET salary = salary * 1.1
WHERE department = 'IT';

DELETE FROM employees
WHERE salary < 3000;

TRUNCATE TABLE employees;
```
## 10. Table Management
```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    department VARCHAR(50),
    salary DECIMAL(10,2),
    hire_date DATE DEFAULT CURRENT_DATE
);

ALTER TABLE employees ADD COLUMN email VARCHAR(255);
ALTER TABLE employees DROP COLUMN email;
ALTER TABLE employees ALTER COLUMN salary TYPE FLOAT;

DROP TABLE employees;

ALTER TABLE employees RENAME TO staff;
```
## 11. Constraints
```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    department VARCHAR(50),
    salary DECIMAL(10,2) CHECK (salary > 0),
    email VARCHAR(255) UNIQUE
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);

```
## 12. Indexes & Performance
```sql
CREATE INDEX idx_emp_dept ON employees(department);

CREATE UNIQUE INDEX idx_unique_email ON employees(email);

DROP INDEX idx_emp_dept;
```
## 13. Views
```sql
CREATE VIEW high_salary_employees AS
SELECT name, department, salary
FROM employees
WHERE salary > 5000;

SELECT * FROM high_salary_employees;

DROP VIEW high_salary_employees;
```
## 14. Transactions
```sql
BEGIN;

UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

COMMIT;

ROLLBACK;
```
## 15. User & Security
```sql
CREATE USER 'username' IDENTIFIED BY 'password';

GRANT SELECT, INSERT ON employees TO username;

REVOKE INSERT ON employees FROM username;

```
## 16. Useful Functions
```sql
-- String
UPPER(name)
LOWER(name)
LENGTH(name)
TRIM(name)
CONCAT(first, ' ', last)
SUBSTRING(name,1,3)

-- Date
CURRENT_DATE
CURRENT_TIMESTAMP
EXTRACT(YEAR FROM hire_date)
DATEADD(YEAR,1,hire_date)      -- SQL Server
hire_date + INTERVAL '1 year'  -- PostgreSQL/MySQL

-- Conditional
CASE
    WHEN salary > 5000 THEN 'High'
    WHEN salary BETWEEN 3000 AND 5000 THEN 'Medium'
    ELSE 'Low'
END AS salary_level
```

# Advanced SQL for Data Analysis

## 17. Case Statements for Categorization
```sql
SELECT name, salary,
CASE
    WHEN salary >= 10000 THEN 'Executive'
    WHEN salary BETWEEN 5000 AND 9999 THEN 'Senior'
    WHEN salary BETWEEN 3000 AND 4999 THEN 'Mid'
    ELSE 'Junior'
END AS salary_band
FROM employees;
```

## 18. Pivoting / Crosstab
```sql
-- Count employees by department (pivot style)
SELECT department,
       SUM(CASE WHEN gender = 'M' THEN 1 ELSE 0 END) AS male_count,
       SUM(CASE WHEN gender = 'F' THEN 1 ELSE 0 END) AS female_count
FROM employees
GROUP BY department;

-- PostgreSQL example with CROSSTAB
-- Requires tablefunc extension
SELECT * FROM crosstab(
  'SELECT department, gender, COUNT(*) FROM employees GROUP BY department, gender ORDER BY 1,2',
  'VALUES (''M''),(''F'')'
) AS ct(department text, male_count int, female_count int);
```

## 19. Percentiles & Quantiles
```sql
-- Median & percentiles using window functions
SELECT DISTINCT
       PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) OVER () AS median_salary,
       PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY salary) OVER () AS p90_salary
FROM employees;
```
## 20. Correlation & Statistical Functions
```sql
-- PostgreSQL example: correlation
SELECT CORR(age, salary) AS correlation_age_salary
FROM employees;

-- Variance & standard deviation
SELECT VAR_POP(salary) AS variance,
       STDDEV_POP(salary) AS std_dev
FROM employees;
```

## 21. Time Series Analysis
```sql
-- Rolling 7-day sales total
SELECT order_date,
       SUM(sales) OVER (ORDER BY order_date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS rolling_7d_sales
FROM orders;

-- Monthly aggregation
SELECT DATE_TRUNC('month', order_date) AS month,
       SUM(sales) AS total_sales
FROM orders
GROUP BY DATE_TRUNC('month', order_date)
ORDER BY month;
```

## 22. Cohort Analysis
```sql
WITH first_purchase AS (
    SELECT customer_id, MIN(order_date) AS cohort_date
    FROM orders
    GROUP BY customer_id
),
cohort_orders AS (
    SELECT o.customer_id,
           f.cohort_date,
           o.order_date,
           EXTRACT(MONTH FROM AGE(o.order_date, f.cohort_date)) AS months_since_cohort
    FROM orders o
    JOIN first_purchase f ON o.customer_id = f.customer_id
)
SELECT cohort_date,
       months_since_cohort,
       COUNT(DISTINCT customer_id) AS active_customers
FROM cohort_orders
GROUP BY cohort_date, months_since_cohort
ORDER BY cohort_date, months_since_cohort;
```

## 23. Ranking & Top-N Per Group
```sql
-- Get top 3 salaries per department
SELECT *
FROM (
    SELECT department, name, salary,
           ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS rn
    FROM employees
) t
WHERE rn <= 3;
```

## 24. Anomaly Detection (Outliers)
```sql
-- Z-score method for outlier detection
SELECT name, salary,
       (salary - AVG(salary) OVER ()) / STDDEV(salary) OVER () AS z_score
FROM employees
WHERE ABS((salary - AVG(salary) OVER ()) / STDDEV(salary) OVER ()) > 3;
```

## 25. Advanced Joins for Analysis
```sql
-- Semi Join (EXISTS)
SELECT name
FROM employees e
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = e.id
);

-- Anti Join (NOT EXISTS)
SELECT name
FROM employees e
WHERE NOT EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = e.id
);
```

## 26. Recursive CTEs
```sql
-- Example: hierarchy (employees & managers)
WITH RECURSIVE employee_hierarchy AS (
    SELECT id, name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL
    UNION ALL
    SELECT e.id, e.name, e.manager_id, h.level + 1
    FROM employees e
    JOIN employee_hierarchy h ON e.manager_id = h.id
)
SELECT * FROM employee_hierarchy;
```

## 27. Data Cleaning Tricks
```sql
-- Handling missing values
SELECT COALESCE(email, 'no_email@domain.com') AS email_cleaned
FROM employees;

-- Removing duplicates (PostgreSQL/SQL Server)
DELETE FROM employees
WHERE id NOT IN (
  SELECT MIN(id)
  FROM employees
  GROUP BY email
);

-- String normalization
SELECT TRIM(LOWER(REPLACE(name, '  ', ' '))) AS cleaned_name
FROM employees;
```