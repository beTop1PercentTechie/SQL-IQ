# SQL Interview Questions and Answers (Subqueries)

Let's use **a real-world scenario**:  
📌 **E-commerce Platform Example**  
We have:
- **Orders Table**: Stores customer orders.
- **Customers Table**: Stores customer details.

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

## 31. What is a subquery in SQL? Where can it be used?

### 🔹 Explanation:
A **subquery** (or nested query) is a query **inside another query**. The result of the subquery is used by the main query.

🔹 **Where Can It Be Used?**
- **In `SELECT` (Derived Values)**: Compute values dynamically.
- **In `FROM` (Derived Tables)**: Treat a subquery as a table.
- **In `WHERE` (Conditional Filtering)**: Compare results from a subquery.
- **In `HAVING` (Filtering Aggregates)**: Filter based on aggregated values.

### 🔹 Example Query:

1️⃣ Find **customers who have placed an order**:
```sql
SELECT name 
FROM customers 
WHERE customer_id IN (SELECT DISTINCT customer_id FROM orders);
```
🔹 **Output:**
| name    |
|---------|
| Alice   |
| Bob     |
| Charlie |

*(David is missing because he hasn't placed an order.)*

---

## 32. What is the difference between a correlated subquery and a non-correlated subquery?

### 🔹 Explanation:

| Type | Description | Runs Once or Per Row? | Example Use Case |
|------|-------------|----------------------|------------------|
| **Non-Correlated Subquery** | Runs **independently** and returns a single result | ✅ Once | Finding customers who placed orders |
| **Correlated Subquery** | Runs **for each row** in the outer query | 🔄 Per Row | Finding customers whose total order is above average |

### 🔹 Example Queries:

1️⃣ **Non-Correlated Subquery**: Find customers who placed orders worth more than **$400**:
```sql
SELECT name 
FROM customers 
WHERE customer_id IN (SELECT customer_id FROM orders WHERE total_amount > 400);
```

### **🔹 Example Output:**
| name    |
|---------|
| Bob     |
| Charlie |

2️⃣ **Correlated Subquery**: Find customers whose **total order amount is greater than the average**:
```sql
SELECT name
FROM customers c
WHERE EXISTS (
  SELECT 1 FROM orders o 
  WHERE o.customer_id = c.customer_id 
  GROUP BY o.customer_id 
  HAVING SUM(o.total_amount) > (SELECT AVG(total_amount) FROM orders)
);
```
### **🔹 Example Output:**
| name    |
|---------|
| Alice   |
| Bob     |
| Charlie |

🔹 **Explanation**:
- The subquery **depends on each row** of `customers`, making it correlated.
- It compares each customer's **total order amount** with the **average order amount**.

---

## 33. How do you use a subquery in the SELECT clause?

### 🔹 Explanation:
A subquery in the `SELECT` clause is used to **retrieve calculated values**.

🔹 **Example Use Case:**  
Retrieve each customer's **name and total amount spent**.

### 🔹 Example Query:
```sql
SELECT c.name,
       (SELECT SUM(o.total_amount) 
        FROM orders o 
        WHERE o.customer_id = c.customer_id) AS total_spent
FROM customers c;
```

🔹 **Output:**
| name    | total_spent |
|---------|------------|
| Alice   | 550.00     |
| Bob     | 500.00     |
| Charlie | 450.00     |
| David   | NULL       |

*(David has NULL because he has never placed an order.)*

---

## 34. Explain how EXISTS and NOT EXISTS work in SQL.

### 🔹 Explanation:

| Operator | Description | Example Use Case |
|----------|-------------|------------------|
| **EXISTS** | Returns `TRUE` if the subquery finds any result | Customers who placed orders |
| **NOT EXISTS** | Returns `TRUE` if the subquery finds **no result** | Customers who haven't placed any order |

### 🔹 Example Queries:

1️⃣ **Using EXISTS**: Find customers who have placed at least one order:
```sql
SELECT name
FROM customers c
WHERE EXISTS (
  SELECT 1 FROM orders o 
  WHERE o.customer_id = c.customer_id
);
```

### **🔹 Example Output:**
| name    |
|---------|
| Alice   |
| Bob     |
| Charlie |


2️⃣ **Using NOT EXISTS**: Find customers who **haven't placed any order**:
```sql
SELECT name
FROM customers c
WHERE NOT EXISTS (
  SELECT 1 FROM orders o 
  WHERE o.customer_id = c.customer_id
);
```

🔹 **Output (Customers without orders):**
| name  |
|-------|
| David |

---

## 35. What are some performance considerations for subqueries?

### 🔹 Explanation:
Subqueries can be **slow** if not optimized properly. Key considerations:

| Issue | Impact | Solution |
|-------|--------|----------|
| **Unoptimized Subqueries** | Slow performance | Use **JOINs** instead |
| **Correlated Subqueries** | Runs for each row (slow) | Avoid if possible |
| **Large Data Sets** | High resource usage | Index key columns |
| **Unnecessary SELECT \*** | Extra computation | Select only needed columns |

### 🔹 Performance Optimization Example:

❌ **Bad (Using Subquery in WHERE Clause)**
```sql
SELECT name
FROM customers
WHERE customer_id IN (SELECT DISTINCT customer_id FROM orders);
```
✅ **Better (Using JOIN)**
```sql
SELECT DISTINCT c.name
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id;
```
🔹 **Why is `JOIN` better?**
- The `JOIN` approach is **faster** because it **avoids scanning the orders table multiple times**.

---

# 📌 Summary Table

| Concept | Explanation | Example Query |
|---------|------------|--------------|
| **Subquery** | Query inside another query | `SELECT * FROM customers WHERE id IN (SELECT customer_id FROM orders);` |
| **Non-Correlated Subquery** | Runs once, independent of outer query | `SELECT * FROM customers WHERE id IN (SELECT customer_id FROM orders);` |
| **Correlated Subquery** | Runs per row, dependent on outer query | `SELECT * FROM customers WHERE EXISTS (SELECT 1 FROM orders WHERE orders.customer_id = customers.id);` |
| **Subquery in SELECT** | Retrieves computed values | `SELECT name, (SELECT SUM(total_amount) FROM orders WHERE customer_id = c.id) FROM customers c;` |
| **EXISTS / NOT EXISTS** | Checks for the existence of related records | `SELECT * FROM customers WHERE EXISTS (SELECT 1 FROM orders WHERE customer_id = customers.id);` |
| **Performance Optimization** | Use JOIN instead of subqueries | `SELECT DISTINCT c.name FROM customers c JOIN orders o ON c.customer_id = o.customer_id;` |

---



