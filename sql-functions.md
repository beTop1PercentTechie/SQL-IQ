# SQL Interview Questions and Answers (Functions)

## 📌 Sample Table and Data Setup

```sql
-- Creating the employees table
CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  department VARCHAR(50),
  salary DECIMAL(10,2),
  age INT,
  joining_date DATE
);

-- Inserting sample records (including one with NULL values)
INSERT INTO employees (id, name, department, salary, age, joining_date) VALUES
(1, 'Alice', 'IT', 60000.00, 30, '2020-05-10'),
(2, 'Bob', 'HR', 55000.00, 40, '2019-08-20'),
(3, 'Charlie', 'IT', 70000.00, 35, '2018-03-15'),
(4, 'David', 'Finance', 50000.00, 45, '2021-11-01'),
(5, 'Eve', 'HR', 45000.00, 28, '2022-07-25'),
(6, 'Frank', NULL, NULL, NULL, NULL); -- New row with NULL values
```

---

## 16. What are aggregate functions? Explain COUNT, SUM, AVG, MIN, and MAX.

### 🔹 Explanation:
Aggregate functions **perform calculations** on multiple rows and return a single value.

| Function | Description | Example |
|----------|-------------|---------|
| `COUNT()` | Returns the number of rows | `COUNT(*)` |
| `SUM()` | Adds numeric values | `SUM(salary)` |
| `AVG()` | Calculates average | `AVG(salary)` |
| `MIN()` | Finds smallest value | `MIN(salary)` |
| `MAX()` | Finds largest value | `MAX(salary)` |

### 🔹 Example Queries:

1️⃣ Count the total number of employees:
```sql
SELECT COUNT(*) AS total_employees FROM employees;
```
🔹 **Output:** `6`

2️⃣ Find the **highest and lowest salary**:
```sql
SELECT MAX(salary) AS highest_salary, MIN(salary) AS lowest_salary FROM employees;
```
🔹 **Output:** `highest_salary = 70000`, `lowest_salary = 45000`  
*(NULL values are ignored by default in aggregate functions.)*

---

## 17. What are scalar functions? Provide examples like LEN, UPPER, LOWER, and SUBSTRING.

### 🔹 Explanation:
Scalar functions **operate on individual values** and return a **single value** per row.

| Function | Description | Example |
|----------|-------------|---------|
| `UPPER()` | Converts text to uppercase | `UPPER(name)` |
| `LOWER()` | Converts text to lowercase | `LOWER(name)` |
| `LEN()` or `LENGTH()` | Returns length of text | `LENGTH(name)` |
| `SUBSTRING()` | Extracts part of a string | `SUBSTRING(name, 1, 3)` |

### 🔹 Example Queries:
Certainly! Below are the **separate outputs** formatted in the requested style.

---

### **1️⃣ Convert all employee names to uppercase:**
#### **Query:**
```sql
SELECT name, UPPER(name) AS upper_name FROM employees LIMIT 3;
```

🔹 **Example Output:**
| name   | upper_name |
|--------|------------|
| Alice  | ALICE      |
| Bob    | BOB        |
| Charlie | CHARLIE   |

---

### **2️⃣ Extract the first three letters of names (LIMIT 3):**
#### **Query:**
```sql
SELECT name, SUBSTRING(name, 1, 3) AS short_name FROM employees LIMIT 3;
```

🔹 **Example Output:**
| name   | short_name |
|--------|------------|
| Alice  | Ali        |
| Bob    | Bob        |
| Charlie | Cha       |

---


## 18. How do you extract the current date and time in SQL?

### 🔹 Explanation:
Different databases provide different functions to fetch the **current date and time**.

| Database | Current Date & Time | Current Date |
|----------|----------------------|-------------|
| MySQL / PostgreSQL | `NOW()` | `CURRENT_DATE` |
| SQL Server | `GETDATE()` | `CONVERT(DATE, GETDATE())` |

Sure! Below are the **separate outputs** for each query, formatted as requested.  

---

### **1️⃣ Get the current date and time:**

#### **Query:**
```sql
SELECT NOW() AS current_datetime;
```

🔹 **Example Output:**
| current_datetime      |
|-----------------------|
| 2024-01-29 12:45:30  |

---

### **2️⃣ Get the current date only:**
#### **Query:**
```sql
SELECT CURRENT_DATE AS today_date;
```

🔹 **Example Output:**
| today_date  |
|------------|
| 2024-01-29 |


---

## 19. Explain the COALESCE function with an example.

### 🔹 Explanation:
The `COALESCE()` function **returns the first non-NULL value** from its arguments.

🔹 **Why use COALESCE?**
- Prevents NULL values from breaking queries.
- Provides a default value when NULL is present.

### 🔹 Example Query:

1️⃣ Get employee salaries, but if **salary is NULL**, show "Not Assigned":
```sql
SELECT name, COALESCE(salary, 'Not Assigned') AS salary_info FROM employees;
```

🔹 **Example Output:**

| name    | salary      |
|---------|------------|
| Alice   | 60000.00   |
| Bob     | 55000.00   |
| Charlie | 70000.00   |
| David   | 50000.00   |
| Eve     | 45000.00   |
| Frank   | Not Assigned |


---

## 20. How do you use CASE statements in SQL?

### 🔹 Explanation:
The `CASE` statement **works like IF-ELSE** in SQL. It allows conditional logic inside queries.

🔹 **Use Cases:**
- Assign custom labels based on conditions.
- Replace complex `IF` conditions.

### 🔹 Example Query:

1️⃣ Categorize employees based on salary:
```sql
SELECT name, salary,
  CASE 
    WHEN salary >= 60000 THEN 'High'
    WHEN salary BETWEEN 50000 AND 59999 THEN 'Medium'
    ELSE 'Low'
  END AS salary_category
FROM employees;
```

🔹 **Example Output:**
🔹 **Example Output:**

| name    | salary      | salary_category |
|---------|------------|----------------|
| Alice   | 60000.00   | High           |
| Bob     | 55000.00   | Medium         |
| Charlie | 70000.00   | High           |
| David   | 50000.00   | Medium         |
| Eve     | 45000.00   | Low            |
| Frank   | NULL       | Low            |


---

## 21. How do you use the ISNULL function, and how is it different from COALESCE?


### **1️⃣ Using `ISNULL()` (SQL Server):**
#### **Query:**
```sql
SELECT name, ISNULL(salary, 0) AS salary FROM employees;
```

🔹 **Example Output:**

| name   | ISNULL salary |
|--------|--------------|
| Alice  | 60000.00     |
| Bob    | 55000.00     |
| Charlie| 70000.00     |
| David  | 50000.00     |
| Eve    | 45000.00     |
| Frank  | 0            |

---

### **2️⃣ Using `COALESCE()`:**
#### **Query:**
```sql
SELECT name, COALESCE(salary, 0) AS salary FROM employees;
```

🔹 **Example Output:**

| name   | COALESCE salary |
|--------|-----------------|
| Alice  | 60000.00        |
| Bob    | 55000.00        |
| Charlie| 70000.00        |
| David  | 50000.00        |
| Eve    | 45000.00        |
| Frank  | 0               |

---

🔹 **Explanation:**
- **Both functions** replace `NULL` with `0` for Frank, but note that:
  - **`ISNULL()`** is specific to **SQL Server**.
  - **`COALESCE()`** works across **multiple database systems** and can handle **multiple columns**.



# 📌 Summary Table

| Function | Purpose | Example |
|----------|---------|---------|
| **Aggregate Functions** | Perform calculations on multiple rows | `SELECT SUM(salary) FROM employees;` |
| **Scalar Functions** | Work on single values | `SELECT UPPER(name) FROM employees;` |
| **Current Date & Time** | Fetch system date and time | `SELECT NOW();` |
| **COALESCE()** | Returns first non-NULL value | `SELECT COALESCE(salary, 0);` |
| **CASE** | Implements conditional logic | `SELECT name, CASE WHEN salary > 60000 THEN 'High' END FROM employees;` |
| **ISNULL()** | Replaces NULL with a default value | `SELECT ISNULL(salary, 0);` |

---
