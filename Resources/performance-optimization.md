# SQL Interview Questions and Answers (Performance Optimization)

Let's use **a real-world scenario**:  
📌 **Logistics & Supply Chain Management Example**  
We have:
- **Warehouses Table**: Stores information about warehouses.
- **Shipments Table**: Stores information about shipments sent from warehouses.

## 📌 Sample Tables and Data Setup

```sql
-- Creating the warehouses table
CREATE TABLE warehouses (
  warehouse_id INT PRIMARY KEY,
  warehouse_name VARCHAR(100),
  location VARCHAR(100),
  capacity INT
);

-- Creating the shipments table
CREATE TABLE shipments (
  shipment_id INT PRIMARY KEY,
  warehouse_id INT,
  product VARCHAR(100),
  quantity INT,
  shipment_date DATE,
  FOREIGN KEY (warehouse_id) REFERENCES warehouses(warehouse_id)
);

-- Inserting sample records into warehouses
INSERT INTO warehouses (warehouse_id, warehouse_name, location, capacity) VALUES
(1, 'Warehouse A', 'New York', 1000),
(2, 'Warehouse B', 'Los Angeles', 1500),
(3, 'Warehouse C', 'Chicago', 1200),
(4, 'Warehouse D', 'Houston', 900);

-- Inserting sample records into shipments
INSERT INTO shipments (shipment_id, warehouse_id, product, quantity, shipment_date) VALUES
(101, 1, 'Laptops', 200, '2024-01-01'),
(102, 2, 'Smartphones', 500, '2024-01-05'),
(103, 1, 'Tablets', 300, '2024-01-10'),
(104, 3, 'Laptops', 450, '2024-01-15'),
(105, 1, 'Smartwatches', 200, '2024-01-20'),
(106, 2, 'Tablets', 700, '2024-01-25');
```

---

## 56. What are some common SQL query optimization techniques?

### 🔹 Explanation:
SQL **query optimization** improves database performance by reducing execution time and resource consumption.

🔹 **Optimization Techniques:**
| Technique | Description | Example |
|-----------|-------------|---------|
| **Indexing** | Speeds up `WHERE`, `JOIN`, and `ORDER BY` operations | `CREATE INDEX idx_product ON shipments(product);` |
| **Avoid SELECT \*** | Fetch only required columns to reduce data load | `SELECT product, quantity FROM shipments;` |
| **Use EXISTS instead of IN** | `EXISTS` is faster than `IN` when checking existence | `SELECT * FROM warehouses WHERE EXISTS (SELECT 1 FROM shipments WHERE warehouse_id = warehouses.warehouse_id);` |
| **Optimize Joins** | Use indexed joins instead of full table scans | `JOIN` on indexed columns |
| **Use Proper Data Types** | Reduce storage and memory overhead | Use `VARCHAR(50)` instead of `TEXT` if a column holds short strings |

### 🔹 Example Query Optimization:

❌ **Bad Query (Slow due to SELECT \*)**
```sql
SELECT * FROM shipments WHERE product = 'Laptops';
```

✅ **Optimized Query (Uses Index and Selects Only Required Columns)**
```sql
CREATE INDEX idx_product ON shipments(product);
SELECT product, quantity FROM shipments WHERE product = 'Laptops';
```

🔹 **Why is this faster?**
- The **index on `product`** speeds up lookups.
- Selecting only `product, quantity` **reduces unnecessary data retrieval**.

---

## 57. How do you analyze a query execution plan?

### 🔹 Explanation:
A **query execution plan** provides insights into **how SQL processes a query**, helping optimize performance.

🔹 **Key Components of Execution Plan:**
| Component | Description |
|-----------|-------------|
| **Seq Scan (Sequential Scan)** | Scans the entire table (slow) |
| **Index Scan** | Uses an index for faster retrieval |
| **Nested Loop Join** | Efficient for small datasets but slow for large joins |
| **Hash Join** | Optimized for large joins, uses hashing |

### 🔹 Example Query:

1️⃣ **Using EXPLAIN in PostgreSQL/MySQL**:
```sql
EXPLAIN ANALYZE
SELECT * FROM shipments WHERE product = 'Laptops';
```

🔹 **Example Output:**
```
Index Scan using idx_product on shipments (cost=0.15..8.23 rows=1 width=16)
```

🔹 **Explanation:**
- **Index Scan** → Uses an index (Fast ✅)
- **Seq Scan** → Reads all rows (Slow ❌)

✅ **Optimize using Indexes**
```sql
CREATE INDEX idx_product ON shipments(product);
```

---

## 58. What are the pros and cons of using indexes for performance?

### 🔹 Explanation:
Indexes **speed up searches** but **slow down inserts and updates**.

🔹 **Pros and Cons of Indexing:**
| Feature | Pros ✅ | Cons ❌ |
|---------|--------|--------|
| **Faster Queries** | Improves search performance (`WHERE`, `JOIN`) | Slows down `INSERT`, `UPDATE`, `DELETE` |
| **Efficient Sorting** | Optimizes `ORDER BY` operations | Takes up additional storage |
| **Speeds Up Joins** | Reduces full table scans | May require maintenance |

### 🔹 Example:
❌ **Without Index:**
```sql
SELECT * FROM shipments WHERE product = 'Tablets';
```
- Full table scan **(Slow ❌)**.

✅ **With Index:**
```sql
CREATE INDEX idx_product ON shipments(product);
```
- Uses index for **fast retrieval (✅)**.

---

## 59. How can you reduce the performance overhead of joins?

### 🔹 Explanation:
Joins are **expensive operations**, especially on large tables. Optimizing joins reduces execution time.

🔹 **Optimization Techniques for Joins:**
| Technique | Description | Example |
|-----------|-------------|---------|
| **Use Indexes on Join Columns** | Faster lookups | `CREATE INDEX idx_warehouse_id ON shipments(warehouse_id);` |
| **Use INNER JOIN Instead of OUTER JOIN (If Possible)** | Returns fewer rows | `SELECT * FROM shipments INNER JOIN warehouses ON shipments.warehouse_id = warehouses.warehouse_id;` |
| **Avoid Joining Large Unfiltered Tables** | Filter before joining | `SELECT * FROM shipments WHERE quantity > 200 JOIN warehouses ON shipments.warehouse_id = warehouses.warehouse_id;` |

### 🔹 Example:
❌ **Slow Join (No Index, No Filter)**
```sql
SELECT * FROM shipments
JOIN warehouses ON shipments.warehouse_id = warehouses.warehouse_id;
```

✅ **Optimized Join (Indexed & Filtered)**
```sql
CREATE INDEX idx_warehouse_id ON shipments(warehouse_id);
SELECT * FROM shipments
JOIN warehouses ON shipments.warehouse_id = warehouses.warehouse_id
WHERE shipments.quantity > 200;
```

---

## 60. What are table partitions, and how do they improve performance?

### 🔹 Explanation:
**Table partitioning** splits a large table into **smaller, manageable pieces**.

🔹 **Types of Partitioning:**
| Type | Description | Example |
|------|-------------|---------|
| **Range Partitioning** | Splits table based on column values | Orders split by date range |
| **List Partitioning** | Splits table based on specific values | Customers split by country |
| **Hash Partitioning** | Distributes rows evenly | Evenly divides large datasets |
| **Composite Partitioning** | Uses multiple partitioning types | Date + Country |

### 🔹 Example Query:

1️⃣ **Creating a Partitioned Orders Table (PostgreSQL/MySQL 8+)**
```sql
CREATE TABLE orders_partitioned (
    order_id INT NOT NULL,
    customer_id INT,
    total_amount DECIMAL(10,2),
    order_date DATE NOT NULL
) 
PARTITION BY RANGE (YEAR(order_date)) (
    PARTITION p_before_2020 VALUES LESS THAN (2020),
    PARTITION p_2020 VALUES LESS THAN (2021),
    PARTITION p_2021 VALUES LESS THAN (2022),
    PARTITION p_2022 VALUES LESS THAN (2023),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);
```



🔹 **Performance Benefits:**

✅ **Faster queries**: Only relevant partitions are scanned.  

✅ **Better manageability**: Old partitions can be archived.  

---

# 📌 Summary Table

| Concept | Explanation | Example Query |
|---------|------------|--------------|
| **Query Optimization** | Improves query speed | `CREATE INDEX idx_product ON shipments(product);` |
| **Execution Plan** | Analyzes query performance | `EXPLAIN ANALYZE SELECT * FROM shipments;` |
| **Indexing Pros & Cons** | Faster reads but slower writes | `CREATE INDEX idx_warehouse ON shipments(warehouse_id);` |
| **Optimizing Joins** | Filters & indexes improve join speed | `SELECT * FROM shipments WHERE quantity > 200 JOIN warehouses;` |
| **Table Partitioning** | Splits large tables for faster access | `PARTITION BY RANGE (order_date);` |

---

