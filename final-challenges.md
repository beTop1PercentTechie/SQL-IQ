# Final Challenges

Let's use **a real-world scenario**:  

📌 **Sales & Retail Data Analysis**  
We have:
- **Sales Table**: Stores product sales across different regions.
- **Products Table**: Stores product details.

## 📌 Sample Tables and Data Setup

```sql
-- Creating the sales table
CREATE TABLE sales (
  sale_id INT PRIMARY KEY AUTO_INCREMENT,
  product VARCHAR(50),
  region VARCHAR(50),
  quantity INT,
  sale_date DATE
);

-- Creating the products table
CREATE TABLE products (
  product_id INT PRIMARY KEY,
  product_name VARCHAR(50),
  category VARCHAR(50),
  price DECIMAL(10,2)
);

-- Inserting sample records into sales
INSERT INTO sales (sale_id, product, region, quantity, sale_date) VALUES
(1, 'Laptop', 'North', 5, '2024-01-01'),
(2, 'Laptop', 'North', 8, '2024-01-02'),
(3, 'Tablet', 'South', 3, '2024-01-02'),
(4, 'Laptop', 'West', 6, '2024-01-03'),
(5, 'Tablet', 'South', 7, '2024-01-04'),
(6, 'Phone', 'North', 10, '2024-01-05');

-- Inserting sample records into products
INSERT INTO products (product_id, product_name, category, price) VALUES
(1, 'Laptop', 'Electronics', 1000.00),
(2, 'Tablet', 'Electronics', 600.00),
(3, 'Phone', 'Electronics', 800.00);
```

---

## 99. Write a query to find the most frequently occurring value in a column.

### 🔹 Explanation:
- To find the **most frequent value**, we use `COUNT(*)` and `ORDER BY COUNT DESC`.
- `LIMIT 1` ensures **only the most frequent value** is retrieved.

### 🔹 Example Query:
```sql
SELECT product, COUNT(*) AS occurrence
FROM sales
GROUP BY product
ORDER BY occurrence DESC
LIMIT 1;
```

🔹 **Example Output:**
| product | occurrence |
|---------|------------|
| Laptop  | 3         |

🔹 **Alternative Query (Handling Ties)**:
```sql
WITH RankedSales AS (
    SELECT product, COUNT(*) AS occurrence,
           RANK() OVER (ORDER BY COUNT(*) DESC) AS rank
    FROM sales
    GROUP BY product
)
SELECT product, occurrence FROM RankedSales WHERE rank = 1;
```

---

## 100. Write a query to pivot data from rows to columns.

### 🔹 Explanation:
- **Pivoting** transforms **row-based data** into **columns**.
- We use **CASE statements** or **`PIVOT` (SQL Server)**.

### 🔹 Example Query (MySQL/PostgreSQL - Using CASE WHEN):
```sql
SELECT region,
       SUM(CASE WHEN product = 'Laptop' THEN quantity ELSE 0 END) AS Laptop_Sales,
       SUM(CASE WHEN product = 'Tablet' THEN quantity ELSE 0 END) AS Tablet_Sales,
       SUM(CASE WHEN product = 'Phone' THEN quantity ELSE 0 END) AS Phone_Sales
FROM sales
GROUP BY region;
```

🔹 **Example Output:**
| region | Laptop_Sales | Tablet_Sales | Phone_Sales |
|--------|-------------|-------------|-------------|
| North  | 13         | 0           | 10          |
| South  | 0          | 10          | 0           |
| West   | 6          | 0           | 0           |

🔹 **Alternative Using PIVOT (SQL Server):**
```sql
SELECT * FROM (
    SELECT region, product, quantity FROM sales
) AS SourceTable
PIVOT (
    SUM(quantity) FOR product IN ([Laptop], [Tablet], [Phone])
) AS PivotTable;
```

---

## 101. Write a query to calculate the difference between consecutive rows.

### 🔹 Explanation:
- We use **`LAG()`** to fetch the **previous row’s value** and compute the difference.

### 🔹 Example Query:
```sql
SELECT sale_id, product, region, quantity,
       LAG(quantity) OVER (PARTITION BY product ORDER BY sale_date) AS prev_quantity,
       quantity - LAG(quantity) OVER (PARTITION BY product ORDER BY sale_date) AS quantity_difference
FROM sales;
```

🔹 **Example Output:**
| sale_id | product | region | quantity | prev_quantity | quantity_difference |
|---------|---------|--------|----------|---------------|---------------------|
| 1  | Laptop  | North  | 5  | NULL | NULL |
| 2  | Laptop  | North  | 8  | 5    | 3    |
| 4  | Laptop  | West   | 6  | 8    | -2   |
| 6  | Phone   | North  | 10 | NULL | NULL |
| 3  | Tablet  | South  | 3  | NULL | NULL |
| 5  | Tablet  | South  | 7  | 3    | 4    |


🔹 **Explanation:**
- `LAG(quantity)` gets the **previous sale's quantity**.
- `quantity_difference` shows **change in sales over time**.

---

## 102. Write a query to rank products by sales within each region.

### 🔹 Explanation:
- We use **`RANK()`** to assign **rank within each region**.

### 🔹 Example Query:
```sql
SELECT region, product, SUM(quantity) AS total_sales,
       RANK() OVER (PARTITION BY region ORDER BY SUM(quantity) DESC) AS rank
FROM sales
GROUP BY region, product;
```

🔹 **Example Output:**
| region | product | total_sales | rank |
|--------|---------|------------|------|
| North  | Phone   | 10         | 1    |
| North  | Laptop  | 8          | 2    |
| South  | Tablet  | 10         | 1    |
| West   | Laptop  | 6          | 1    |

🔹 **Explanation:**
- **Ranks products by total sales per region**.
- **Ties get the same rank, but next rank is skipped**.

---

## 103. Write a query to merge data from two tables with matching and non-matching rows.

### 🔹 Explanation:
- A **FULL OUTER JOIN** merges data, including **unmatched records** from both tables.

### 🔹 Example Query:
```sql
SELECT p.product_name, p.category, s.region, s.quantity
FROM products p
FULL OUTER JOIN sales s ON p.product_name = s.product;
```

🔹 **Example Output:**
| product_name | category    | region | quantity |
|-------------|------------|--------|----------|
| Laptop      | Electronics | North  | 5        |
| Laptop      | Electronics | West   | 6        |
| Phone       | Electronics | North  | 10       |
| Tablet      | Electronics | South  | 10       |
| NULL        | NULL        | NULL   | NULL     |

🔹 **Explanation:**
- **Merges products and sales** data.
- **NULL values** appear for non-matching records.

---

## 104. Write a query to calculate the cumulative percentage of sales by product.

### 🔹 Explanation:
- We use **`SUM()` with `OVER()`** to compute **cumulative sales percentage**.

### 🔹 Example Query:
```sql
WITH ProductSales AS (
    SELECT product, SUM(quantity) AS total_sales
    FROM sales
    GROUP BY product
),
CumulativeSales AS (
    SELECT product, total_sales,
           SUM(total_sales) OVER (ORDER BY total_sales DESC) AS cumulative_sales,
           SUM(total_sales) OVER () AS grand_total
    FROM ProductSales
)
SELECT product, total_sales, 
       ROUND(100 * cumulative_sales / grand_total, 2) AS cumulative_percentage
FROM CumulativeSales;
```

🔹 **Example Output:**
| product | total_sales | cumulative_percentage |
|---------|------------|-----------------------|
| Laptop  | 19         | 48.72%                |
| Tablet  | 10         | 100.00%                |
| Phone   | 10         | 100.00%               |

🔹 **Explanation:**
- **Calculates cumulative percentage of total sales**.
- **Useful for Pareto analysis (80/20 rule in sales).**

---
