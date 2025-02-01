# SQL Interview Questions and Answers (Window Functions)

Let's use **a real-world scenario**:  
📌 **E-commerce Platform Example**  
We have:
- **Orders Table**: Stores customer orders with purchase details.

## 📌 Sample Table and Data Setup

```sql
-- Creating the orders table
CREATE TABLE orders (
  order_id INT PRIMARY KEY,
  customer_id INT,
  total_amount DECIMAL(10,2),
  order_date DATE
);

-- Inserting sample records into orders
INSERT INTO orders (order_id, customer_id, total_amount, order_date) VALUES
(101, 1, 250.00, '2024-01-01'),
(102, 2, 500.00, '2024-01-05'),
(103, 1, 300.00, '2024-01-10'),
(104, 3, 450.00, '2024-01-15'),
(105, 1, 200.00, '2024-01-20'),
(106, 2, 700.00, '2024-01-25');
```

---

## 52. What are window functions in SQL? Provide examples.

### 🔹 Explanation:
**Window functions** perform calculations across a **set of rows** related to the current row **without collapsing them** into a single result.

🔹 **Why Use Window Functions?**
- **Unlike GROUP BY, it retains individual rows** while applying calculations.
- **Performs ranking, running totals, and comparisons across rows.**

🔹 **Common Window Functions:**
| Function | Purpose |
|----------|---------|
| `ROW_NUMBER()` | Assigns a unique rank to each row |
| `RANK()` | Assigns rank, allowing duplicate ranks with gaps |
| `DENSE_RANK()` | Assigns rank without gaps |
| `LEAD()` | Fetches next row’s value |
| `LAG()` | Fetches previous row’s value |

### 🔹 Example Query:
Find the **running total of sales per customer**:
```sql
SELECT customer_id, order_id, total_amount,
       SUM(total_amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS running_total
FROM orders;
```

🔹 **Example Output:**
| customer_id | order_id | total_amount | running_total |
|------------|---------|-------------|--------------|
| 1          | 101     | 250.00      | 250.00       |
| 1          | 103     | 300.00      | 550.00       |
| 1          | 105     | 200.00      | 750.00       |
| 2          | 102     | 500.00      | 500.00       |
| 2          | 106     | 700.00      | 1200.00      |
| 3          | 104     | 450.00      | 450.00       |


🔹 **Explanation:**
- `PARTITION BY customer_id` → Resets sum for each customer.
- `ORDER BY order_date` → Computes running total in order of purchases.

---

## 53. Explain ROW_NUMBER(), RANK(), and DENSE_RANK() functions.

### 🔹 Explanation:
These functions **assign rankings to rows** based on specified columns.

| Function | Description | Allows Duplicate Ranks? | Skips Rank After Ties? |
|----------|------------|-------------------------|------------------------|
| **ROW_NUMBER()** | Assigns a **unique** rank to each row | ❌ No | ✅ Yes |
| **RANK()** | Assigns the same rank to **duplicates**, skipping next rank | ✅ Yes | ✅ Yes |
| **DENSE_RANK()** | Assigns the same rank to duplicates **without gaps** | ✅ Yes | ❌ No |

### 🔹 Example Query:
```sql
SELECT customer_id, order_id, total_amount,
       ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS row_num,
       RANK() OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS rank_num,
       DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS dense_rank_num
FROM orders;
```

🔹 **Example Output:**
| customer_id | order_id | total_amount | row_num | rank_num | dense_rank_num |
|------------|---------|-------------|---------|----------|---------------|
| 1          | 103     | 300.00      | 1       | 1        | 1             |
| 1          | 101     | 250.00      | 2       | 2        | 2             |
| 1          | 105     | 200.00      | 3       | 3        | 3             |
| 2          | 106     | 700.00      | 1       | 1        | 1             |
| 2          | 102     | 500.00      | 2       | 2        | 2             |
| 3       | 104     | 450.00  | 1       | 1       | 1       |

🔹 **Key Takeaways:**
- `ROW_NUMBER()` **never duplicates ranks**.
- `RANK()` **skips numbers when duplicates exist**.
- `DENSE_RANK()` **does not skip numbers**.

---

## 54. What is the difference between PARTITION BY and GROUP BY?

### 🔹 Explanation:

| Clause | Purpose | Keeps Individual Rows? | Works with Aggregate & Window Functions? |
|--------|---------|------------------------|------------------------------------------|
| **GROUP BY** | Aggregates data into a **single row per group** | ❌ No | ✅ Yes (SUM, AVG, COUNT) |
| **PARTITION BY** | Groups data for **window functions** without collapsing rows | ✅ Yes | ✅ Yes (Works with `OVER()`) |

### 🔹 Example Queries:

1️⃣ **Using GROUP BY** (Aggregates total sales per customer):
```sql
SELECT customer_id, SUM(total_amount) AS total_sales
FROM orders
GROUP BY customer_id;
```
🔹 **Output:**
| customer_id | total_sales |
|------------|------------|
| 1          | 750.00     |
| 2          | 1200.00    |
| 3          | 450.0      |

2️⃣ **Using PARTITION BY** (Retains individual rows while calculating totals):
```sql
SELECT customer_id, order_id, total_amount,
       SUM(total_amount) OVER (PARTITION BY customer_id) AS total_sales
FROM orders;
```
🔹 **Output:**
| customer_id | order_id | total_amount | total_sales |
|------------|---------|-------------|------------|
| 1          | 101     | 250.00      | 750.00     |
| 1          | 103     | 300.00      | 750.00     |
| 1          | 105     | 200.00      | 750.00     |
| 2          | 102     | 500.00      | 1200.00    |
| 2          | 106     | 700.00      | 1200.00    |
| 3	         |  104	   | 450.00	     | 450.00     |

🔹 **Key Difference:**  
- `GROUP BY` collapses rows.
- `PARTITION BY` keeps rows but **calculates aggregates within each group**.

---

## 55. How do LEAD and LAG functions work in SQL?

### 🔹 Explanation:
| Function | Purpose | Example Use Case |
|----------|---------|------------------|
| **LEAD()** | Fetches **next row’s value** in the partition | Finding next purchase amount |
| **LAG()** | Fetches **previous row’s value** in the partition | Finding previous purchase amount |

### 🔹 Example Query:

Find **previous and next purchases for each customer**:
```sql
SELECT customer_id, order_id, total_amount,
       LAG(total_amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS prev_order,
       LEAD(total_amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS next_order
FROM orders;
```

🔹 **Example Output:**
| customer_id | order_id | total_amount | prev_order | next_order |
|------------|---------|-------------|------------|------------|
| 1          | 101     | 250.00      | NULL       | 300.00     |
| 1          | 103     | 300.00      | 250.00     | 200.00     |
| 1          | 105     | 200.00      | 300.00     | NULL       |
| 2          | 102     | 500.00      | NULL       | 700.00     |
| 2          | 106     | 700.00      | 500.00     | NULL       |
| 3	         | 104	   | 450.00		   | NULL       |  NULL      | 

🔹 **Key Takeaways:**
- `LAG()` fetches **previous row’s value**.
- `LEAD()` fetches **next row’s value**.
- Useful for **time-series analysis**.

---

