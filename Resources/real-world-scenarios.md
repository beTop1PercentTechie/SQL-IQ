# SQL Interview Questions and Answers (Real-World Scenarios)

Let's use **a real-world scenario**:  

📌 **HR & Payroll System Example**  
We have:
- **Employees Table**: Stores employee details including manager relationships.

## 📌 Sample Table and Data Setup

```sql
-- Creating the employees table
CREATE TABLE employees (
  employee_id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(50),
  department VARCHAR(50),
  salary DECIMAL(10,2),
  manager_id INT NULL
);

-- Inserting sample records (including duplicates)
INSERT INTO employees (employee_id, name, department, salary, manager_id) VALUES
(1, 'Alice', 'IT', 70000, NULL),      -- CEO (No Manager)
(2, 'Bob', 'IT', 60000, 1),           -- Reports to Alice
(3, 'Charlie', 'Finance', 80000, NULL), -- CFO (No Manager)
(4, 'David', 'Finance', 75000, 3),    -- Reports to Charlie
(5, 'Eve', 'HR', 50000, NULL),        -- HR Head (No Manager)
(6, 'Frank', 'IT', 60000, 2),         -- Reports to Bob
(7, 'Bob', 'IT', 60000, 1),           -- Duplicate Bob
(8, 'Grace', 'Finance', 75000, 3);    -- Duplicate David
```

---

## 71. Write a query to find duplicate records in a table.

### 🔹 Explanation:
To find **duplicate records**, we use `GROUP BY` with `HAVING COUNT(*) > 1`.

🔹 **Steps:**
- Identify duplicate values based on `name`, `department`, and `salary`.
- Use `GROUP BY` to group identical records.
- Use `HAVING COUNT(*) > 1` to filter duplicates.

### 🔹 Example Query:
```sql
SELECT name, department, salary, COUNT(*)
FROM employees
GROUP BY name, department, salary
HAVING COUNT(*) > 1;
```

🔹 **Output:**
| name  | department | salary | count |
|-------|-----------|--------|-------|
| Bob   | IT        | 60000  | 2     |


🔹 **Explanation:**
- The result shows employee **Bob** has duplicate records.

---

## 72. How do you delete duplicate records while retaining one?

### 🔹 Explanation:
We use `ROW_NUMBER()` to assign a unique rank to duplicates and delete rows **except for the first occurrence**.

🔹 **Steps:**
- Use `ROW_NUMBER()` to assign a **unique** ID to each duplicate.
- Keep only rows where `ROW_NUMBER() = 1`.

### 🔹 Example Query:
```sql
WITH DuplicateRecords AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY name, department, salary ORDER BY employee_id) AS rn
    FROM employees
)
DELETE FROM employees WHERE employee_id IN (
    SELECT employee_id FROM DuplicateRecords WHERE rn > 1
);
```

🔹 **Explanation:**
- **`PARTITION BY`** groups duplicates.
- **`ROW_NUMBER()`** assigns ranks to each duplicate.
- **`rn > 1`** ensures we **delete duplicates while keeping one**.

---

## 73. Write a query to find the second-highest salary in a table.

### 🔹 Explanation:
To find the **second-highest salary**, we use **`DISTINCT`, `LIMIT`, or `OFFSET`**.

### 🔹 Example Query (Using `OFFSET`):
```sql
SELECT DISTINCT salary 
FROM employees 
ORDER BY salary DESC 
LIMIT 1 OFFSET 1;
```

🔹 **Alternative Using `MAX()` and Subquery**:
```sql
SELECT MAX(salary) AS second_highest_salary
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

🔹 **Example Output:**
| second_highest_salary |
|----------------------|
| 75000               |

🔹 **Explanation:**
- `MAX(salary)` inside the subquery gets the **highest salary**.
- The outer query finds the **maximum of remaining salaries**.

---

## 74. Write a query to find employees who earn more than their managers.

### 🔹 Explanation:
- Employees have **manager_id** referring to another employee.
- We use **self-join** to compare employee salaries with their manager's salary.

### 🔹 Example Query:
```sql
SELECT e.name AS employee, e.salary AS employee_salary, 
       m.name AS manager, m.salary AS manager_salary
FROM employees e
JOIN employees m ON e.manager_id = m.employee_id
WHERE e.salary > m.salary;
```

🔹 **Example Output:**
| employee | employee_salary | manager | manager_salary |
|----------|----------------|---------|----------------|




---

## 75. How do you fetch the top-N rows from a table?

### 🔹 Explanation:
To fetch the **top-N rows**, we use:
- `LIMIT` (MySQL, PostgreSQL)
- `FETCH FIRST N ROWS` (SQL Server, Oracle)
- `TOP N` (SQL Server)

### 🔹 Example Query:

1️⃣ **MySQL / PostgreSQL**:
```sql
SELECT * FROM employees ORDER BY salary DESC LIMIT 3;
```

2️⃣ **SQL Server / Oracle**:
```sql
SELECT * FROM employees ORDER BY salary DESC FETCH FIRST 3 ROWS ONLY;
```

3️⃣ **Using `RANK()` for Top-N**:
```sql
WITH RankedSalaries AS (
    SELECT *, RANK() OVER (ORDER BY salary DESC) AS rank
    FROM employees
)
SELECT * FROM RankedSalaries WHERE rank <= 3;
```

🔹 **Example Output:**
| employee_id  | name   | department | salary   | manager_id |
|---- |--------|-----------|--------- |------------|
| 3   | Charlie | Finance   | 80000.00 | -          |
| 4   | David   | Finance   | 75000.00 | 3          |
| 8   | Grace   | Finance   | 75000.00 | 3          |


🔹 **Explanation:**
- The **first two queries** use `LIMIT` or `FETCH FIRST` for the **top 3 highest salaries**.
- The **third query** uses `RANK()` to handle ties.

---

# 📌 Summary Table

| Query | Explanation | Example Query |
|-------|------------|--------------|
| **Find Duplicates** | Groups duplicate values using `HAVING COUNT(*) > 1` | `SELECT name, COUNT(*) FROM employees GROUP BY name HAVING COUNT(*) > 1;` |
| **Delete Duplicates** | Uses `ROW_NUMBER()` to remove duplicates | `DELETE FROM employees WHERE employee_id IN (SELECT employee_id FROM DuplicateRecords WHERE rn > 1);` |
| **Find Second-Highest Salary** | Uses `MAX()` and subquery | `SELECT MAX(salary) FROM employees WHERE salary < (SELECT MAX(salary) FROM employees);` |
| **Find Employees Earning More Than Managers** | Uses self-join to compare salaries | `SELECT e.name, e.salary FROM employees e JOIN employees m ON e.manager_id = m.employee_id WHERE e.salary > m.salary;` |
| **Fetch Top-N Rows** | Uses `LIMIT`, `FETCH FIRST N ROWS`, or `RANK()` | `SELECT * FROM employees ORDER BY salary DESC LIMIT 3;` |

---

### 🚀 Key Takeaways:
1️⃣ **Use `HAVING COUNT(*) > 1` to detect duplicates and `ROW_NUMBER()` to remove them**.  
2️⃣ **Use `MAX()` and subqueries to find the second-highest salary**.  
3️⃣ **Self-joins help find employees earning more than their managers**.  
4️⃣ **Use `LIMIT`, `FETCH FIRST N ROWS`, or `RANK()` for fetching top-N rows**.  

---

