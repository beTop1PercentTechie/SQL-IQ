# SQL Interview Questions and Answers (Grouping and Aggregation)

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

-- Inserting sample records
INSERT INTO employees (id, name, department, salary, age, joining_date) VALUES
(1, 'Alice', 'IT', 60000.00, 30, '2020-05-10'),
(2, 'Bob', 'HR', 55000.00, 40, '2019-08-20'),
(3, 'Charlie', 'IT', 70000.00, 35, '2018-03-15'),
(4, 'David', 'Finance', 50000.00, 45, '2021-11-01'),
(5, 'Eve', 'HR', 45000.00, 28, '2022-07-25'),
(6, 'Frank', 'IT', 62000.00, 29, '2023-06-30'),
(7, 'Grace', 'Finance', 53000.00, 42, '2017-09-18'),
(8, 'Henry', 'IT', 58000.00, 31, '2022-03-22');
```

---

## 22. What is the difference between WHERE and HAVING clauses?

### 🔹 Explanation:
Both `WHERE` and `HAVING` are used to filter records, but they serve **different purposes**:

| Clause  | Works On | Used With | Filters Before/After Aggregation? |
|---------|---------|-----------|----------------------------------|
| `WHERE` | **Rows** (individual records) | `SELECT`, `UPDATE`, `DELETE` | Filters **before** aggregation |
| `HAVING` | **Groups** (aggregated results) | `GROUP BY` | Filters **after** aggregation |

### 🔹 Example Query:

---

### **1️⃣ Using `WHERE` to filter individual records before aggregation**
#### **Query:**
```sql
SELECT * FROM employees WHERE department = 'IT';
```

🔹 **Example Output:**
| employee_id | name   | department | salary  | age | join_date  |
|------------|--------|-----------|---------|-----|------------|
| 1          | Alice  | IT        | 60000.00 | 30  | 2020-05-10 |
| 3          | Charlie| IT        | 70000.00 | 35  | 2018-03-15 |
| 6          | Frank  | IT        | 62000.00 | 29  | 2023-06-30 |
| 8          | Henry  | IT        | 58000.00 | 31  | 2022-03-22 |

🔹 **Explanation:**  
- The `WHERE` clause filters only **employees in the IT department**.
- **No aggregations** are applied yet.

---

### **2️⃣ Using `HAVING` to filter aggregated data**
#### **Query:**
```sql
SELECT department, AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 55000;
```

🔹 **Example Output:**
| department | avg_salary |
|------------|------------|
| IT         | 62500.00   |

🔹 **Explanation:**  
- The `GROUP BY` groups salaries **by department**.
- The `HAVING` clause filters **only departments where the average salary > 55000**.
- Only **IT qualifies**, as its average salary is **(60000 + 70000 + 62000 + 58000) / 4 = 62500.00**.


---

## 23. How do you use GROUP BY in SQL? Provide an example.

### 🔹 Explanation:
The `GROUP BY` clause is used to **group rows** that have the same values in specified columns and perform **aggregate functions** on them.

### 🔹 Syntax:
```sql
SELECT column_name, AGGREGATE_FUNCTION(column_name)
FROM table_name
GROUP BY column_name;
```

### 🔹 Example Query:
Find the **total salary per department**:
```sql
SELECT department, SUM(salary) AS total_salary
FROM employees
GROUP BY department;
```

🔹 **Example Output:**
| department | total_salary |
|------------|-------------|
| IT         | 250000.00   |
| HR         | 100000.00   |
| Finance    | 103000.00   |

🔹 **Explanation:**
- The `GROUP BY department` groups employees by their **department**.
- The `SUM(salary)` calculates the **total salary** for each department.

---

## 24. Explain the ROLLUP and CUBE operations in SQL.

## 🔹 Explanation
Both `ROLLUP` and `CUBE` are **advanced aggregation techniques** used for **summary reports and data analysis**.

| Operation | Description | Example Use Case |
|-----------|------------|------------------|
| **ROLLUP** | Generates **hierarchical summaries** | Sales total for each department and a **grand total** |
| **CUBE** | Generates **all possible combinations** of groupings | Sales total for **each department, age group, and combinations** |

---

## **1️⃣ Using `ROLLUP` (Generates Subtotals and Grand Total)**

### ✅ Query:
```sql
SELECT department, SUM(salary) AS total_salary
FROM employees
GROUP BY ROLLUP(department);
```

### 🔹 Example Output:
| department | total_salary |
|------------|-------------|
| IT         | 250000.00   |
| HR         | 100000.00   |
| Finance    | 103000.00   |
| **NULL**   | **453000.00** (Grand Total) |

### 🔹 Explanation:
✅ `ROLLUP(department)` generates **subtotals** for each **department**.  
✅ The final **`NULL` row represents the grand total** of all salaries across departments.

---

## **2️⃣ Using `CUBE` (Generates All Combinations of Aggregates)**

### **✅ Query:**
```sql
SELECT department, age, SUM(salary) AS total_salary
FROM employees
GROUP BY CUBE(department, age);
```

### **🔹 Example Output (CUBE in PostgreSQL, SQL Server, Oracle):**
### 🔹 Example Output:
| department | age | total_salary |
|------------|-----|-------------|
| IT         | 30  | 60000.00    |
| IT         | 35  | 70000.00    |
| IT         | 29  | 62000.00    |
| IT         | 31  | 58000.00    |
| IT         | NULL | 250000.00   |
| HR         | 40  | 55000.00    |
| HR         | 28  | 45000.00    |
| HR         | NULL | 100000.00   |
| Finance    | 45  | 50000.00    |
| Finance    | 42  | 53000.00    |
| Finance    | NULL | 103000.00   |
| NULL       | 30  | 60000.00    |
| NULL       | 40  | 55000.00    |
| NULL       | 35  | 70000.00    |
| NULL       | 45  | 50000.00    |
| NULL       | 28  | 45000.00    |
| NULL       | 29  | 62000.00    |
| NULL       | 42  | 53000.00    |
| NULL       | 31  | 58000.00    |
| NULL       | NULL | 453000.00   |

### **🔹 Explanation:**
✅ `CUBE(department, age)` **computes all possible totals**, including:  
- **Total salary per department**.  
- **Total salary per age group**.  
- **Grand Total (`NULL, NULL`) for all employees**.

---

## 🔹 **Note: `ROLLUP` vs. `CUBE`**
| Feature | `ROLLUP` | `CUBE` |
|---------|---------|-------|
| **Summary Type** | **Hierarchical Subtotals** | **All Possible Aggregations** |
| **Grand Total Included?** | ✅ Yes | ✅ Yes |
| **Subtotals for Each Column?** | ✅ Yes | ✅ Yes |
| **Best For** | **Summarizing reports by hierarchy (e.g., yearly → monthly → daily sales)** | **Comparing all aggregations (e.g., by department & age group)** |

---

## **🛑 `CUBE()` is Not Supported in MySQL!**
### ✅ **Alternatives in MySQL**
Since MySQL **does not support `CUBE()`**, use:
1️⃣ **`ROLLUP()`** → If you only need hierarchical subtotals.  
2️⃣ **`UNION ALL`** → To manually compute all combinations.

### **✅ Alternative Query for MySQL (Simulating `CUBE()` with `UNION ALL`):**
```sql
SELECT department, age, SUM(salary) AS total_salary
FROM employees
GROUP BY department, age
UNION ALL
SELECT department, NULL, SUM(salary) AS total_salary
FROM employees
GROUP BY department
UNION ALL
SELECT NULL, age, SUM(salary) AS total_salary
FROM employees
GROUP BY age
UNION ALL
SELECT NULL, NULL, SUM(salary) AS total_salary
FROM employees;
```

🔹 **This manually simulates `CUBE()` functionality in MySQL**.

---

### 🎯 **Key Takeaways**
1️⃣ **Use `ROLLUP()` when you need hierarchical subtotals**.  
2️⃣ **Use `CUBE()` for full aggregation across multiple dimensions**.  
3️⃣ **MySQL does not support `CUBE()`**, so use `UNION ALL` instead.  
4️⃣ **Both `ROLLUP` and `CUBE` help summarize data in reports.**  

---



## 25. How can you filter aggregated data using the HAVING clause?

### 🔹 Explanation:
The `HAVING` clause filters **aggregated values**. It is used with `GROUP BY` and **cannot be used without an aggregate function**.

### 🔹 Example Query:
Find **departments where the total salary exceeds 100,000**:
```sql
SELECT department, SUM(salary) AS total_salary
FROM employees
GROUP BY department
HAVING SUM(salary) > 100000;
```
🔹 **Example Output:**
| department | total_salary |
|------------|-------------|
| IT         | 250000.00   |
| Finance    | 103000.00   |

### 🔹 Explanation:
- `SUM(salary) > 100000` filters **departments** where the total salary is greater than **100,000**.
- Unlike `WHERE`, `HAVING` applies **after** aggregation.

---

# 📌 Summary Table

| Clause/Operation | Purpose | Example |
|------------------|---------|---------|
| **WHERE** | Filters individual rows **before** aggregation | `WHERE department = 'IT'` |
| **HAVING** | Filters grouped data **after** aggregation | `HAVING SUM(salary) > 100000` |
| **GROUP BY** | Groups records by specified columns | `GROUP BY department` |
| **ROLLUP** | Generates hierarchical summary rows | `GROUP BY ROLLUP(department)` |
| **CUBE** | Generates all possible grouping combinations | `GROUP BY CUBE(department, age)` |

---



