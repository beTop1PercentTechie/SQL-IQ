# SQL Interview Questions and Answers (Views)

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

## 40. What are views in SQL, and how are they used?

### 🔹 Explanation:
A **view** in SQL is a **virtual table** based on the result of a query. It does **not store data** itself but provides an abstraction for easy access.

🔹 **Why Use Views?**
- **Simplifies complex queries**: Allows reusability of frequently used queries.
- **Enhances security**: Users can be given access to views without exposing full tables.
- **Ensures data consistency**: Helps standardize access to the database.

🔹 **Use Cases:**
- Showing **only required columns** to specific users.
- Pre-aggregating data for **reporting**.
- Masking sensitive data.

### 🔹 Example Query:

1️⃣ **Creating a View** to show **customer orders**:
```sql
CREATE VIEW customer_orders AS
SELECT c.name, o.total_amount, o.order_date
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id;
```

2️⃣ **Using the View**:
```sql
SELECT * FROM customer_orders;
```

🔹 **Example Output:**
| name    | total_amount | order_date |
|---------|-------------|------------|
| Alice   | 250.00      | 2024-01-01 |
| Bob     | 500.00      | 2024-01-05 |
| Alice   | 300.00      | 2024-01-10 |
| Charlie | 450.00      | 2024-01-15 |

---

## 41. What is the difference between a view and a materialized view?

### 🔹 Explanation:

| Feature | View | Materialized View |
|---------|------|------------------|
| **Data Storage** | No storage, retrieves fresh data on query execution | Stores the results of a query physically in the database for faster retrieval |
| **Speed** | Slower (fetches live data) | Faster (precomputed results) |
| **Use Case** | Ideal for dynamic, always up-to-date data | Ideal for performance-critical reports |
| **Refresh Mechanism** | Always up-to-date | Needs manual or scheduled refresh |

### 🔹 Example Queries:

1️⃣ **Creating a Materialized View** (Only in databases like PostgreSQL, Oracle)
```sql
CREATE MATERIALIZED VIEW sales_summary AS
SELECT customer_id, SUM(total_amount) AS total_spent
FROM orders
GROUP BY customer_id;
```

2️⃣ **Refreshing the Materialized View** (To update stored data)
```sql
REFRESH MATERIALIZED VIEW sales_summary;
```

🔹 **When to Use Materialized Views?**
- **Performance optimization** for expensive queries.
- **Precomputed aggregations** for dashboards.

---

## 42. How do you update data through a view?

### 🔹 Explanation:
- Some views allow `INSERT`, `UPDATE`, and `DELETE`, but **only if they follow certain rules**.
- **A view must directly reference a single table** to be updatable.

🔹 **Conditions for Updatable Views:**
✅ View should reference **one base table**.  
✅ View should not use **aggregations (`SUM()`, `AVG()`)**.  
✅ View should not use **DISTINCT, GROUP BY, HAVING, or JOINs**.

### 🔹 Example Queries:

1️⃣ **Creating an Updatable View**:
```sql
CREATE VIEW customer_info AS
SELECT customer_id, name, country FROM customers;
```
2️⃣ **Run Select query** 
```sql
SELECT * FROM customer_info WHERE customer_id = 3;
```
🔹 **Example Output (Before Updating  Data)**:
```sql
SELECT * FROM customers WHERE customer_id = 3;
```
| customer_id | name    | country        |
|-------------|--------|---------------|
| 3           | Charlie | UK |

2️⃣ **Updating Through the View**:
```sql
UPDATE customer_info SET country = 'United Kingdom' WHERE customer_id = 3;
```

3️⃣ **Example Output (Checking Updated Data)**:
```sql
SELECT * FROM customers WHERE customer_id = 3;
```
| customer_id | name    | country        |
|-------------|--------|---------------|
| 3           | Charlie | United Kingdom |

---

## 43. Can you create an indexed view? Explain with an example.

### 🔹 Explanation:
- **Indexed views** (also called **materialized views with indexing**) store **precomputed results**.
- **Improves performance** by indexing **precomputed data**.
- Supported in **SQL Server** and **Oracle**.

🔹 **Rules for Indexed Views:**
✅ Must use `SCHEMABINDING` to prevent table schema changes.  
✅ Cannot use `DISTINCT, UNION, GROUP BY, HAVING, or Subqueries`.  

### 🔹 Example Query (SQL Server):

1️⃣ **Creating an Indexed View**:
```sql
CREATE VIEW order_totals WITH SCHEMABINDING AS
SELECT customer_id, COUNT(*) AS order_count, SUM(total_amount) AS total_spent
FROM dbo.orders
GROUP BY customer_id;
```

2️⃣ **Creating an Index on the View**:
```sql
CREATE UNIQUE CLUSTERED INDEX idx_order_totals ON order_totals(customer_id);
```

🔹 **Performance Benefit:**  
- Queries using the view now **use the index** instead of recalculating data.

3️⃣ **Using the Indexed View**:
```sql
SELECT * FROM order_totals WHERE total_spent > 400;
```

🔹 **Example Output:**
| customer_id | order_count | total_spent |
|------------|------------|-------------|
| 1          | 2          | 550.00      |
| 2          | 1          | 500.00      |

---

# 📌 Summary Table

| Feature | Explanation | Example Query |
|---------|------------|--------------|
| **View** | Virtual table without storing data | `CREATE VIEW customer_orders AS SELECT name, total_amount FROM orders;` |
| **Materialized View** | Stores precomputed data | `CREATE MATERIALIZED VIEW sales_summary AS SELECT SUM(total_amount) FROM orders;` |
| **Updatable View** | Allows updates on base table | `UPDATE customer_info SET country = 'UK' WHERE customer_id = 3;` |
| **Indexed View** | View with a physical index for performance | `CREATE UNIQUE CLUSTERED INDEX idx_view ON view_name(column);` |

---

