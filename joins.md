# SQL Interview Questions and Answers (Joins)


## 📌 Sample Tables and Data Setup

```sql
-- Creating the employees table
CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  department_id INT,
  salary DECIMAL(10,2)
);

-- Creating the departments table
CREATE TABLE departments (
  department_id INT PRIMARY KEY,
  department_name VARCHAR(50)
);

-- Inserting sample records into employees
INSERT INTO employees (id, name, department_id, salary) VALUES
(1, 'Alice', 1, 60000.00),
(2, 'Bob', 2, 55000.00),
(3, 'Charlie', 1, 70000.00),
(4, 'David', 3, 50000.00),
(5, 'Eve', 2, 45000.00),
(6, 'Frank', NULL, 62000.00);

-- Inserting sample records into departments
INSERT INTO departments (department_id, department_name) VALUES
(1, 'IT'),
(2, 'HR'),
(3, 'Finance'),
(4, 'Marketing'); -- Department with no employees
```

---

## 26. What are joins in SQL? Why are they used?

### 🔹 Explanation:
A **JOIN** in SQL is used to **combine data** from two or more tables based on a related column.

🔹 **Why Use Joins?**
- **Avoid Data Duplication:** Instead of repeating department names in every employee record, we store them separately and join when needed.
- **Retrieve Meaningful Data:** We can fetch related records from different tables efficiently.

🔹 **Types of Joins in SQL:**
1. **INNER JOIN** – Returns matching records from both tables.
2. **OUTER JOIN** – Includes unmatched records:
   - **LEFT JOIN** – Includes unmatched records from the left table.
   - **RIGHT JOIN** – Includes unmatched records from the right table.
   - **FULL OUTER JOIN** – Includes unmatched records from both tables.
3. **SELF JOIN** – Joins a table with itself.
4. **CROSS JOIN** – Returns the Cartesian product of two tables.

---

## 27. Explain the differences between INNER JOIN and OUTER JOIN.

| Type of JOIN | Description | Includes Unmatched Records? |
|-------------|-------------|----------------------------|
| **INNER JOIN** | Returns only matching records from both tables | ❌ No |
| **OUTER JOIN** | Returns matching and unmatched records | ✅ Yes |

### 🔹 Example Queries:

**INNER JOIN** (Returns only matching rows)
```sql
SELECT employees.name, departments.department_name
FROM employees
INNER JOIN departments ON employees.department_id = departments.department_id;
```
🔹 **Output:**
| name    | department_name |
|---------|----------------|
| Alice   | IT             |
| Bob     | HR             |
| Charlie | IT             |
| David   | Finance        |
| Eve     | HR             |

*(Frank is missing because he has NULL in department_id, and Marketing is missing because no employees belong to it.)*



---

## 28. What is the difference between LEFT JOIN, RIGHT JOIN, and FULL OUTER JOIN?

### 🔹 Explanation:
| Type of JOIN | Description | Includes NULLs From? |
|-------------|-------------|----------------------|
| **LEFT JOIN** | Returns all rows from the **left table**, plus matching rows from the right table | ✅ Right Table |
| **RIGHT JOIN** | Returns all rows from the **right table**, plus matching rows from the left table | ✅ Left Table |
| **FULL OUTER JOIN** | Returns all rows from both tables, filling NULL where no match exists | ✅ Both Tables |

### 🔹 Example Queries:

1️⃣ **LEFT JOIN** (Returns all employees and their department names, including those without a department)
```sql
SELECT employees.name, departments.department_name
FROM employees
LEFT JOIN departments ON employees.department_id = departments.department_id;
```
🔹 **Output:**
| name    | department_name |
|---------|----------------|
| Alice   | IT             |
| Bob     | HR             |
| Charlie | IT             |
| David   | Finance        |
| Eve     | HR             |
| Frank   | NULL           |

*(Frank appears with NULL because he has no department.)*

2️⃣ **RIGHT JOIN** (Returns all departments and their employees, even if no employees belong to them)
```sql
SELECT employees.name, departments.department_name
FROM employees
RIGHT JOIN departments ON employees.department_id = departments.department_id;
```
🔹 **Output:**
| name    | department_name |
|---------|----------------|
| Alice   | IT             |
| Bob     | HR             |
| Charlie | IT             |
| David   | Finance        |
| Eve     | HR             |
| NULL    | Marketing      |

*(Marketing appears with NULL because no employees belong to it.)*

3️⃣ **FULL OUTER JOIN** (Returns all employees and all departments)
```sql
SELECT employees.name, departments.department_name
FROM employees
FULL OUTER JOIN departments ON employees.department_id = departments.department_id;
```
🔹 **Output:**
| name    | department_name |
|---------|----------------|
| Alice   | IT             |
| Bob     | HR             |
| Charlie | IT             |
| David   | Finance        |
| Eve     | HR             |
| Frank   | NULL           |
| NULL    | Marketing      |

*(Both Frank and Marketing appear.)*

---

## 29. What is a SELF JOIN? Provide an example use case.

### 🔹 Explanation:
A **SELF JOIN** is a join where a table is **joined with itself**.

🔹 **Use Case:**  
- Finding an employee's **manager** when employees and managers are stored in the same table.

### 🔹 Example Query:
Find employees and their managers:
```sql
SELECT e1.name AS employee, e2.name AS manager
FROM employees e1
LEFT JOIN employees e2 ON e1.department_id = e2.id;
```
🔹 **Example Output:**
| employee | manager |
|---------|---------|
| Alice   | NULL    |
| Bob     | NULL    |
| Charlie | NULL    |

*(If managers were employees too, they would appear in the manager column.)*

---

## 30. Explain the CROSS JOIN and when to use it.

### 🔹 Explanation:
A **CROSS JOIN** returns the **Cartesian product** of two tables. Every row in the first table is paired with every row in the second table.

🔹 **Use Cases:**
- Generating **all possible combinations** of two datasets (e.g., products and colors).

### 🔹 Example Query:
Find all possible **employee-department pairs**:
```sql
SELECT employees.name, departments.department_name
FROM employees
CROSS JOIN departments;
```
🔹 **Output:**
| name    | department  |
|---------|------------|
| Alice   | Marketing  |
| Alice   | Finance    |
| Alice   | HR         |
| Alice   | IT         |
| Bob     | Marketing  |
| Bob     | Finance    |
| Bob     | HR         |
| Bob     | IT         |
| Charlie | Marketing  |
| Charlie | Finance    |
| Charlie | HR         |
| Charlie | IT         |
| David   | Marketing  |
| David   | Finance    |
| David   | HR         |
| David   | IT         |
| Eve     | Marketing  |
| Eve     | Finance    |
| Eve     | HR         |
| Eve     | IT         |
| Frank   | Marketing  |
| Frank   | Finance    |
| Frank   | HR         |
| Frank   | IT         |


*(Every employee is paired with every department.)*

---

# 📌 Summary Table

| JOIN Type | Description | Example Query |
|-----------|-------------|--------------|
| **INNER JOIN** | Returns matching records only | `SELECT * FROM A INNER JOIN B ON A.id = B.id;` |
| **LEFT JOIN** | Returns all from left table + matches from right | `SELECT * FROM A LEFT JOIN B ON A.id = B.id;` |
| **RIGHT JOIN** | Returns all from right table + matches from left | `SELECT * FROM A RIGHT JOIN B ON A.id = B.id;` |
| **FULL OUTER JOIN** | Returns all records, filling NULLs where no match | `SELECT * FROM A FULL OUTER JOIN B ON A.id = B.id;` |
| **SELF JOIN** | Joins a table to itself | `SELECT * FROM A A1 JOIN A A2 ON A1.id = A2.manager_id;` |
| **CROSS JOIN** | Cartesian product of two tables | `SELECT * FROM A CROSS JOIN B;` |

---

