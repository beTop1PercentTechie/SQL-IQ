# SQL Interview Questions and Answers (Operators)

To better understand these SQL concepts, let's create a **common sample table** called `employees` and insert some records.

## 📌 Sample Table and Data Setup

```sql
-- Creating the employees table
CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  department VARCHAR(50),
  salary DECIMAL(10,2),
  age INT
);

-- Inserting sample records
INSERT INTO employees (id, name, department, salary, age) VALUES
(1, 'Alice', 'IT', 60000.00, 30),
(2, 'Bob', 'HR', 55000.00, 40),
(3, 'Charlie', 'IT', 70000.00, 35),
(4, 'David', 'Finance', 50000.00, 45),
(5, 'Eve', 'HR', 45000.00, 28);
```

---

## 11. What are SQL comparison operators? Provide examples.

### 🔹 Explanation:
Comparison operators are used in SQL to **filter records based on conditions**.

| Operator | Description | Example |
|----------|-------------|---------|
| `=`  | Equal to | `salary = 60000` |
| `<>` or `!=` | Not equal to | `department <> 'IT'` |
| `<`  | Less than | `age < 35` |
| `>`  | Greater than | `salary > 50000` |
| `<=` | Less than or equal to | `age <= 40` |
| `>=` | Greater than or equal to | `salary >= 55000` |

### 🔹 Example Query:
Fetch employees who earn **more than 50,000**:
```sql
SELECT * FROM employees WHERE salary > 50000;
```
🔹 **Output:**
| id | name    | department | salary  | age |
|----|--------|------------|---------|----|
| 1  | Alice  | IT         | 60000.00 | 30 |
| 2  | Bob    | HR         | 55000.00 | 40 |
| 3  | Charlie | IT        | 70000.00 | 35 |

---

## 12. How do LIKE and ILIKE operators differ?

### 🔹 Explanation:
Both `LIKE` and `ILIKE` are used for **pattern matching** in text searches.

| Operator | Description |
|----------|-------------|
| `LIKE` | Case-sensitive pattern matching |
| `ILIKE` | Case-insensitive pattern matching (Only available in PostgreSQL) |

### 🔹 Wildcards Used:
| Wildcard | Description | Example |
|----------|-------------|---------|
| `%` | Matches any number of characters | `'Al%'` matches 'Alice', 'Albert' |
| `_` | Matches a single character | `'A__ce'` matches 'Alice' but not 'Al' |

### 🔹 Example Query:
Find employees whose names start with **'A'**:
```sql
SELECT * FROM employees WHERE name LIKE 'A%';
```
🔹 **Output:**
| id | name  | department | salary  | age |
|----|------|------------|---------|----|
| 1  | Alice | IT        | 60000.00 | 30 |

---

## 13. Explain the use of BETWEEN and IN operators.

### 🔹 Explanation:
- **BETWEEN**: Filters results **within a range** (inclusive of boundary values).
- **IN**: Filters results based on a **list of values**.

### 🔹 Example Queries:

1️⃣ Find employees whose salary is **between 50,000 and 65,000**:
```sql
SELECT * FROM employees WHERE salary BETWEEN 50000 AND 65000;
```
🔹 **Output:**
| id | name | department | salary  | age |
|----|------|------------|---------|----|
| 1  | Alice | IT       | 60000.00 | 30 |
| 2  | Bob  | HR       | 55000.00 | 40 |
| 4  | David | Finance | 50000.00 | 45 |

2️⃣ Find employees working in **HR or Finance**:
```sql
SELECT * FROM employees WHERE department IN ('HR', 'Finance');
```
🔹 **Output:**
| id | name  | department | salary  | age |
|----|------|------------|---------|----|
| 2  | Bob  | HR        | 55000.00 | 40 |
| 4  | David | Finance  | 50000.00 | 45 |
| 5  | Eve  | HR        | 45000.00 | 28 |

---

## 14. What is the difference between UNION and UNION ALL?

### 🔹 Explanation:
Both `UNION` and `UNION ALL` combine results from multiple queries.

| Operator | Description |
|----------|-------------|
| `UNION` | Removes duplicates and returns unique rows |
| `UNION ALL` | Returns all records, including duplicates |

### 🔹 Example Queries:

1️⃣ **Using UNION** (Removes duplicates):
```sql
SELECT department FROM employees
UNION
SELECT 'IT' AS department;
```
🔹 **Output (Unique Departments):**
| department |
|------------|
| IT         |
| HR         |
| Finance    |

2️⃣ **Using UNION ALL** (Keeps duplicates):
```sql
SELECT department FROM employees
UNION ALL
SELECT 'IT' AS department;
```
🔹 **Output (Duplicates Included):**
| department |
|------------|
| IT         |
| HR         |
| IT         |
| Finance    |
| HR         |
| IT         |

---

## 15. Explain the difference between the DISTINCT and GROUP BY clauses.

### 🔹 Explanation:
- `DISTINCT` removes duplicate values from a **single column or multiple columns**.
- `GROUP BY` groups records and is often used with **aggregate functions** (SUM, COUNT, AVG, etc.).

### 🔹 Example Queries:

1️⃣ **Using DISTINCT**:
```sql
SELECT DISTINCT department FROM employees;
```
🔹 **Output (Unique Departments):**
| department |
|------------|
| IT         |
| HR         |
| Finance    |

2️⃣ **Using GROUP BY with COUNT**:
```sql
SELECT department, COUNT(*) AS total_employees
FROM employees
GROUP BY department;
```
🔹 **Output (Count per Department):**
| department | total_employees |
|------------|----------------|
| IT         | 2              |
| HR         | 2              |
| Finance    | 1              |

---

# 📌 Summary Table

| Operator | Purpose | Example |
|----------|---------|---------|
| **Comparison Operators (`=`, `>`, `<`, `!=`)** | Filters records based on conditions | `WHERE salary > 50000` |
| **LIKE & ILIKE** | Pattern matching | `WHERE name LIKE 'A%'` |
| **BETWEEN** | Filters within a range | `WHERE salary BETWEEN 50000 AND 70000` |
| **IN** | Matches values in a list | `WHERE department IN ('IT', 'HR')` |
| **UNION** | Combines results and removes duplicates | `SELECT department FROM employees UNION SELECT 'IT'` |
| **UNION ALL** | Combines results including duplicates | `SELECT department FROM employees UNION ALL SELECT 'IT'` |
| **DISTINCT** | Removes duplicate records | `SELECT DISTINCT department FROM employees` |
| **GROUP BY** | Groups records, used with aggregates | `SELECT department, COUNT(*) FROM employees GROUP BY department` |

---

