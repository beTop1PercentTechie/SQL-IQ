# SQL Interview Questions and Answers (Database Design and Security)

Let's use **a real-world scenario**:  
📌 **E-commerce Platform Example**  
We have:
- **Users Table**: Stores user accounts.
- **Orders Table**: Stores order transactions.

## 📌 Sample Tables and Data Setup

```sql
-- Creating the users table
CREATE TABLE users (
  user_id INT PRIMARY KEY,
  username VARCHAR(50) UNIQUE,
  email VARCHAR(100) UNIQUE,
  password_hash VARCHAR(255),
  role VARCHAR(20) CHECK (role IN ('admin', 'customer'))
);

-- Creating the orders table
CREATE TABLE orders (
  order_id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT,
  product VARCHAR(100),
  quantity INT,
  order_date DATE,
  FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- Inserting sample records into users
INSERT INTO users (user_id, username, email, password_hash, role) VALUES
(1, 'alice', 'alice@example.com', 'hashed_password', 'customer'),
(2, 'bob', 'bob@example.com', 'hashed_password', 'customer'),
(3, 'admin_user', 'admin@example.com', 'hashed_password', 'admin');
```

---

## 76. What are primary keys and foreign keys?

### 🔹 Explanation:

| Key Type | Description | Example |
|----------|-------------|---------|
| **Primary Key (PK)** | A column (or set of columns) that **uniquely identifies each row** in a table. | `user_id` in `users` table |
| **Foreign Key (FK)** | A column in one table that **references the primary key** of another table. | `user_id` in `orders` table |

### 🔹 Example Query:

1️⃣ **Primary Key Example:**
```sql
CREATE TABLE users (
  user_id INT PRIMARY KEY, -- Ensures uniqueness
  username VARCHAR(50) NOT NULL
);
```

2️⃣ **Foreign Key Example:**
```sql
CREATE TABLE orders (
  order_id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(user_id) -- Ensures referential integrity
);
```

🔹 **Why Use Foreign Keys?**
✅ **Maintains referential integrity**.  
✅ **Prevents orphaned records** (e.g., Orders cannot exist without a valid User).  

---

## 77. What are unique constraints, and how do they differ from primary keys?

### 🔹 Explanation:
A **unique constraint** ensures that **values in a column are unique**, but **unlike primary keys, it allows NULL values**.

🔹 **Key Differences:**

| Feature | Primary Key | Unique Constraint |
|---------|------------|------------------|
| **Uniqueness** | ✅ Must be unique | ✅ Must be unique |
| **NULL Allowed?** | ❌ No | ✅ Yes (Unless `NOT NULL`) |
| **Number Per Table** | 1 per table | Multiple allowed |

### 🔹 Example Query:

1️⃣ **Primary Key Example:**
```sql
CREATE TABLE users (
  user_id INT PRIMARY KEY,
  username VARCHAR(50) UNIQUE
);
```

2️⃣ **Unique Constraint Example:**
```sql
CREATE TABLE employees (
  employee_id INT PRIMARY KEY,
  email VARCHAR(100) UNIQUE -- Ensures email uniqueness
);
```

🔹 **Use Case:**
- **Primary Key**: Ensures every row **has a unique identifier**.
- **Unique Constraint**: Prevents duplicate **email addresses** in the system.

---

## 78. What is a composite key? Provide an example.

### 🔹 Explanation:
A **composite key** is a **primary key that consists of two or more columns**.

🔹 **Use Cases:**
- **Many-to-Many Relationships**.
- **Multi-column Uniqueness** (e.g., Prevent duplicate **product orders** for a user).

### 🔹 Example Query:
**Orders Table with a Composite Key:**
```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id), -- Composite Key
  FOREIGN KEY (order_id) REFERENCES orders(order_id),
  FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

🔹 **Explanation:**
- The **combination of `order_id` and `product_id`** uniquely identifies each row.
- Prevents **duplicate product entries** within the same order.

---

## 79. How do you ensure database security through user roles and permissions?

### 🔹 Explanation:
SQL provides **roles and permissions** to **control access to data**.

🔹 **Security Measures:**
| Security Feature | Description | Example |
|------------------|-------------|---------|
| **User Roles** | Assign specific permissions | `GRANT SELECT ON users TO customer;` |
| **Least Privilege** | Users should have **only necessary permissions** | Customers cannot modify payments |
| **Encryption** | Encrypt sensitive data like passwords | Store `password_hash`, not plaintext |
| **Audit Logs** | Track all user actions | Log login attempts, modifications |

### 🔹 Example Query:

1️⃣ **Creating Roles & Granting Permissions**
```sql
CREATE ROLE customer;
GRANT SELECT ON orders TO customer; -- Customers can only view orders

CREATE ROLE admin;
GRANT ALL PRIVILEGES ON orders TO admin; -- Admins have full access
```

2️⃣ **Assigning Role to a User**
```sql
GRANT customer TO 'alice';
```

🔹 **Use Case:**
- **Customers** can only **view orders**.
- **Admins** can **modify order details**.

---

## 80. What is SQL injection, and how can it be prevented?

### 🔹 Explanation:
**SQL Injection** is a cyber attack where **malicious SQL queries** are inserted into input fields.

🔹 **Example of SQL Injection Attack:**
```sql
SELECT * FROM users WHERE username = 'admin' OR 1=1; -- Always returns true
```

🔹 **Consequences of SQL Injection:**
❌ **Unauthorized Access**  
❌ **Data Manipulation**  
❌ **Data Leaks**  

### 🔹 How to Prevent SQL Injection:

| Technique | Description | Example |
|-----------|-------------|---------|
| **Use Prepared Statements** | Separates SQL code from data | ✅ Secure |
| **Use Parameterized Queries** | Uses placeholders for user input | ✅ Secure |
| **Validate User Input** | Restricts special characters | ✅ Secure |

### 🔹 Example Query (Using Prepared Statements in MySQL):
```sql
PREPARE stmt FROM 'SELECT * FROM users WHERE username = ? AND password = ?';
EXECUTE stmt USING 'alice', 'secure_password';
```

🔹 **Why is This Secure?**
✅ **SQL is not concatenated directly with input**.  
✅ **User input is treated as data, not code**.  

---

# 📌 Summary Table

| Concept | Explanation | Example Query |
|---------|------------|--------------|
| **Primary Key** | Uniquely identifies each row | `PRIMARY KEY (user_id)` |
| **Foreign Key** | Establishes relationships between tables | `FOREIGN KEY (user_id) REFERENCES users(user_id)` |
| **Unique Constraint** | Ensures uniqueness but allows NULL | `email VARCHAR(100) UNIQUE` |
| **Composite Key** | Multi-column primary key | `PRIMARY KEY (order_id, product_id)` |
| **User Roles & Permissions** | Grants specific access to users | `GRANT SELECT ON orders TO customer;` |
| **SQL Injection Prevention** | Uses prepared statements | `PREPARE stmt FROM 'SELECT * FROM users WHERE username = ?';` |

---
