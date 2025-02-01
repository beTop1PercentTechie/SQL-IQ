# SQL Rapid-Fire Quiz: Questions & Answers

---

## 87. What is the default sorting order in SQL?

### 🔹 Explanation:
- The **default sorting order in SQL is ASCENDING (`ASC`)**.
- If `ORDER BY` is not specified, SQL may return results in an **arbitrary order** based on execution plans.

### 🔹 Example Query:
```sql
SELECT * FROM employees ORDER BY salary; -- Default is ASC
```

✅ **To explicitly sort in descending order**:
```sql
SELECT * FROM employees ORDER BY salary DESC;
```

---

## 88. Can you use an aggregate function in the WHERE clause?

### 🔹 Explanation:
❌ **No, aggregate functions (`SUM()`, `AVG()`, `COUNT()`) cannot be used in the `WHERE` clause** because `WHERE` filters rows **before** aggregation.

✅ **Use `HAVING` instead of `WHERE` for filtering aggregates**.

### 🔹 Example Query (Incorrect ❌):
```sql
SELECT department, SUM(salary)
FROM employees
WHERE SUM(salary) > 50000; -- ❌ This will cause an error
```

✅ **Correct Approach (Using HAVING ✅):**
```sql
SELECT department, SUM(salary)
FROM employees
GROUP BY department
HAVING SUM(salary) > 50000;
```

---

## 89. How do you retrieve unique rows from a table?

### 🔹 Explanation:
Use **`DISTINCT`** to remove duplicate records.

### 🔹 Example Query:
```sql
SELECT DISTINCT department FROM employees;
```

🔹 **Alternative Using GROUP BY:**
```sql
SELECT department FROM employees GROUP BY department;
```

---

## 90. Can a table have multiple primary keys?

### 🔹 Explanation:
❌ **No, a table can have only ONE primary key**, but it can be **composite** (using multiple columns).

### 🔹 Example Query (Correct ✅ - Composite Primary Key):
```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id) -- Composite Key
);
```

---

## 91. What is the difference between DELETE and TRUNCATE?

| Feature | DELETE | TRUNCATE |
|---------|--------|----------|
| **Removes Rows?** | ✅ Yes, specific rows | ✅ Yes, all rows |
| **Can Use WHERE?** | ✅ Yes | ❌ No |
| **Speed** | ❌ Slower | ✅ Faster |
| **Auto-Increment Reset?** | ❌ No | ✅ Yes |

### 🔹 Example Queries:

1️⃣ **DELETE (Specific Rows)**:
```sql
DELETE FROM employees WHERE department = 'IT';
```

2️⃣ **TRUNCATE (Removes All Data, Resets Auto-Increment)**:
```sql
TRUNCATE TABLE employees;
```

---

## 92. How do you rename a table in SQL?

### 🔹 Explanation:
Use **`ALTER TABLE ... RENAME TO`** in MySQL/PostgreSQL or **`sp_rename`** in SQL Server.

### 🔹 Example Query:
```sql
ALTER TABLE employees RENAME TO staff;
```

🔹 **SQL Server Version:**
```sql
EXEC sp_rename 'employees', 'staff';
```

---

## 93. What is the purpose of the AUTO_INCREMENT property?

### 🔹 Explanation:
- **AUTO_INCREMENT** automatically **generates unique numbers** for `PRIMARY KEY` fields.
- Commonly used for **ID columns**.

### 🔹 Example Query:
```sql
CREATE TABLE employees (
  employee_id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(50)
);
```

🔹 **Inserting Data:**
```sql
INSERT INTO employees (name) VALUES ('Alice'); -- employee_id = 1
INSERT INTO employees (name) VALUES ('Bob'); -- employee_id = 2
```

---

## 94. What is the difference between CROSS JOIN and FULL OUTER JOIN?

| Join Type | Description | Returns Unmatched Rows? |
|-----------|-------------|------------------------|
| **CROSS JOIN** | Cartesian product (combines all rows) | ❌ No |
| **FULL OUTER JOIN** | Returns all records from both tables | ✅ Yes |

### 🔹 Example Queries:

1️⃣ **CROSS JOIN (Cartesian Product)**
```sql
SELECT * FROM employees CROSS JOIN departments;
```
🔹 **Combines every employee with every department**.

2️⃣ **FULL OUTER JOIN (Includes NULLs)**
```sql
SELECT * FROM employees FULL OUTER JOIN departments ON employees.department_id = departments.department_id;
```
🔹 **Includes employees without departments & departments without employees**.

---

## 95. Can you perform a join on multiple tables? How?

### 🔹 Explanation:
✅ **Yes**, we can join **multiple tables** using `JOIN` statements.

### 🔹 Example Query (Joining 3 Tables):
```sql
SELECT e.name, d.department_name, o.order_date
FROM employees e
JOIN departments d ON e.department_id = d.department_id
JOIN orders o ON e.employee_id = o.employee_id;
```

🔹 **This joins employees, departments, and orders.**

---

## 96. How do you calculate the running total in SQL?

### 🔹 Explanation:
Use **`SUM() OVER(PARTITION BY ... ORDER BY ...)`**.

### 🔹 Example Query:
```sql
SELECT employee_id, salary,
       SUM(salary) OVER (ORDER BY employee_id) AS running_total
FROM employees;
```

🔹 **Calculates a cumulative sum of salaries ordered by employee ID.**

---

## 97. What is the purpose of the SQL LIMIT clause?

### 🔹 Explanation:
- `LIMIT` **restricts the number of rows returned** in MySQL/PostgreSQL.
- SQL Server uses `TOP`, while Oracle uses `FETCH FIRST`.

### 🔹 Example Queries:

1️⃣ **MySQL/PostgreSQL:**
```sql
SELECT * FROM employees LIMIT 5;
```

2️⃣ **SQL Server:**
```sql
SELECT TOP 5 * FROM employees;
```

3️⃣ **Oracle:**
```sql
SELECT * FROM employees FETCH FIRST 5 ROWS ONLY;
```

---

## 98. Explain the purpose of the DEFAULT constraint in SQL.

### 🔹 Explanation:
The **`DEFAULT`** constraint assigns a **default value** if no value is specified.

### 🔹 Example Query:
```sql
CREATE TABLE employees (
  employee_id INT PRIMARY KEY,
  name VARCHAR(50),
  country VARCHAR(50) DEFAULT 'USA'
);
```

🔹 **Inserting Data Without `country`**:
```sql
INSERT INTO employees (employee_id, name) VALUES (1, 'Alice');
```

🔹 **Output:**
| employee_id | name  | country |
|------------|------|---------|
| 1          | Alice | USA     |

---

# 📌 Summary Table

| Question | Explanation | Example Query |
|----------|------------|--------------|
| **Default Sorting Order** | **Ascending (`ASC`)** | `ORDER BY salary ASC;` |
| **Aggregate Functions in WHERE?** | ❌ No, Use HAVING | `HAVING SUM(salary) > 50000;` |
| **Retrieve Unique Rows** | Use `DISTINCT` | `SELECT DISTINCT department FROM employees;` |
| **Multiple Primary Keys?** | ❌ No, but **Composite Key is allowed** | `PRIMARY KEY (order_id, product_id);` |
| **DELETE vs TRUNCATE** | DELETE keeps structure, TRUNCATE resets table | `TRUNCATE TABLE employees;` |
| **Rename Table** | Use `ALTER TABLE` or `sp_rename` | `ALTER TABLE employees RENAME TO staff;` |
| **AUTO_INCREMENT?** | Auto-generates unique IDs | `AUTO_INCREMENT PRIMARY KEY` |
| **CROSS JOIN vs FULL OUTER JOIN** | CROSS JOIN = Cartesian product, FULL JOIN = All records | `CROSS JOIN` and `FULL OUTER JOIN` |
| **Join Multiple Tables?** | Yes, using multiple `JOIN` clauses | `JOIN table1 JOIN table2` |
| **Running Total?** | Use `SUM() OVER()` | `SUM(salary) OVER(ORDER BY employee_id)` |
| **LIMIT Clause?** | Restricts the number of rows returned | `LIMIT 5` |
| **DEFAULT Constraint?** | Assigns a default value if none is provided | `DEFAULT 'USA'` |

---
