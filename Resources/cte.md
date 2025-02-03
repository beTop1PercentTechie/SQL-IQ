# SQL Interview Questions and Answers (Common Table Expressions - CTEs)

Let's use **a real-world scenario**:  
📌 **E-commerce Platform Example**  
We have:
- **Customers Table**: Stores customer details.
- **Orders Table**: Stores customer orders.

## 📌 Sample Tables and Data Setup

```sql
-- Creating the customers table
CREATE TABLE customers (
  customer_id INT PRIMARY KEY,
  name VARCHAR(50),
  country VARCHAR(50)
);

-- Creating the orders table
CREATE TABLE orders (
  order_id INT PRIMARY KEY,
  customer_id INT,
  total_amount DECIMAL(10,2),
  order_date DATE,
  FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

-- Inserting sample records into customers
INSERT INTO customers (customer_id, name, country) VALUES
(1, 'Alice', 'USA'),
(2, 'Bob', 'Canada'),
(3, 'Charlie', 'UK'),
(4, 'David', 'Australia');

-- Inserting sample records into orders
INSERT INTO orders (order_id, customer_id, total_amount, order_date) VALUES
(101, 1, 250.00, '2024-01-01'),
(102, 2, 500.00, '2024-01-05'),
(103, 1, 300.00, '2024-01-10'),
(104, 3, 450.00, '2024-01-15');
```

---

## 49. What is a CTE in SQL? Provide an example.

### 🔹 Explanation:
A **Common Table Expression (CTE)** is a **temporary result set** that improves the readability and maintainability of complex queries.

🔹 **Why Use CTEs?**
- Makes complex queries **more readable**.
- Allows **modular query design**.
- Avoids repeated calculations.

🔹 **Use Cases:**
- Breaking down **nested subqueries**.
- Organizing **complex aggregations**.
- **Recursive queries** for hierarchical data.

### 🔹 Example Query:

1️⃣ **Using a CTE to Find High-Value Customers** (Customers with total purchases above $500):
```sql
WITH customer_totals AS (
    SELECT c.name, SUM(o.total_amount) AS total_spent
    FROM customers c
    JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY c.name
)
SELECT * FROM customer_totals WHERE total_spent > 500;
```

🔹 **Example Output:**
| name    | total_spent |
|---------|------------|
| Alice   | 550.00     |

🔹 **Explanation:**
- The **CTE (`customer_totals`)** first **aggregates** orders per customer.
- The **main query** then **filters out customers who spent more than $500**.

---

## 50. How do recursive CTEs work? Provide a use case.

### 🔹 Explanation:
A **recursive CTE** is a CTE that **calls itself** to process **hierarchical data**.

🔹 **Use Cases:**
- **Organizational hierarchy (employees and managers).**
- **Tree structures (categories, folder systems).**
- **Graph data (finding shortest paths).**

🔹 **Recursive CTE Structure:**
A **recursive CTE** consists of:
1. **Anchor Query** (Base Case) – Retrieves the first level of data.
2. **Recursive Query** – Calls the CTE itself.

### 🔹 Example Use Case: **Employee Hierarchy**
Imagine a company where:
- `id` represents an **employee**.
- `manager_id` represents the **employee's manager**.

#### 📌 Sample Employee Table:
```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    manager_id INT NULL
);
```

#### 📌 Inserting Sample Data:
```sql
INSERT INTO employees (id, name, manager_id) VALUES
(1, 'CEO', NULL),          -- CEO has no manager
(2, 'Manager A', 1),       -- Reports to CEO
(3, 'Manager B', 1),       -- Reports to CEO
(4, 'Employee 1', 2),      -- Reports to Manager A
(5, 'Employee 2', 2),      -- Reports to Manager A
(6, 'Employee 3', 3);      -- Reports to Manager B
```

#### 📌 Recursive CTE to Find Employee Hierarchy:
```sql
WITH RECURSIVE EmployeeHierarchy AS (
    -- Anchor Query: Select the CEO (Top Level)
    SELECT id, name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive Query: Get employees reporting to previous level
    SELECT e.id, e.name, e.manager_id, eh.level + 1
    FROM employees e
    INNER JOIN EmployeeHierarchy eh ON e.manager_id = eh.id
)
SELECT * FROM EmployeeHierarchy;
```

🔹 **Example Output:**
| id | name       | manager_id | level |
|----|-----------|-----------|-------|
| 1  | CEO       | NULL      | 1     |
| 2  | Manager A | 1         | 2     |
| 3  | Manager B | 1         | 2     |
| 4  | Employee 1 | 2         | 3     |
| 5  | Employee 2 | 2         | 3     |
| 6  | Employee 3 | 3         | 3     |

🔹 **Explanation:**
- The **base query selects the `CEO`**.
- The **recursive query selects employees** who **report to the previous level**.
- This continues **until no more employees are found**.

---

## 51. What are the advantages of using CTEs over subqueries?

### 🔹 Explanation:
CTEs improve **readability**, **performance**, and **maintainability** over traditional **subqueries**.

| Feature | CTEs | Subqueries |
|---------|------|-----------|
| **Readability** | ✅ Improves readability | ❌ Harder to understand in nested queries |
| **Reusability** | ✅ Can be referenced multiple times in the query | ❌ Cannot be reused |
| **Performance** | ✅ May improve optimization in large queries | ❌ Executes separately every time |
| **Recursion** | ✅ Supports recursive queries | ❌ Not possible |

### 🔹 Example Query (Using CTE vs. Subquery):

#### ❌ **Without CTE (Using a Subquery)**
```sql
SELECT name, total_spent FROM (
    SELECT c.name, SUM(o.total_amount) AS total_spent
    FROM customers c
    JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY c.name
) AS customer_totals
WHERE total_spent > 500;
```

#### ✅ **With CTE (More Readable & Reusable)**
```sql
WITH customer_totals AS (
    SELECT c.name, SUM(o.total_amount) AS total_spent
    FROM customers c
    JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY c.name
)
SELECT * FROM customer_totals WHERE total_spent > 500;
```

🔹 **Key Takeaways:**
- **CTE is easier to read & understand**.
- **CTEs can be reused multiple times in the query**.
- **CTEs allow recursion**, which subqueries **cannot** do.

---

# 📌 Summary Table

| Concept | Explanation | Example Query |
|---------|------------|--------------|
| **CTE (Common Table Expression)** | A temporary result set for modular queries | `WITH cte AS (SELECT * FROM table) SELECT * FROM cte;` |
| **Recursive CTE** | Calls itself to process hierarchical data | `WITH recursive cte AS (...)` |
| **Advantages of CTEs** | Improves readability, reusability, and performance | `WITH cte AS (...) SELECT * FROM cte;` |

---

