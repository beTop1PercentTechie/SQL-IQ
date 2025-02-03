# SQL Interview Questions and Answers (Procedures, Functions, Triggers & Normalization)

Let's use **a real-world scenario**:  
📌 **Banking System Example**  
We have:
- **Accounts Table**: Stores account balances.
- **Transactions Table**: Stores transaction history.

## 📌 Sample Tables and Data Setup

```sql
-- Creating the accounts table
CREATE TABLE accounts (
  account_id INT PRIMARY KEY,
  account_holder VARCHAR(50),
  balance DECIMAL(10,2)
);

-- Creating the transactions table
CREATE TABLE transactions (
  transaction_id INT PRIMARY KEY AUTO_INCREMENT,
  account_id INT,
  amount DECIMAL(10,2),
  transaction_type VARCHAR(10), -- 'DEPOSIT' or 'WITHDRAW'
  transaction_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (account_id) REFERENCES accounts(account_id)
);

-- Inserting sample records into accounts
INSERT INTO accounts (account_id, account_holder, balance) VALUES
(1, 'Alice', 1000.00),
(2, 'Bob', 2000.00),
(3, 'Charlie', 500.00);
```

---

## 61. What are stored procedures, and how do they differ from functions?

### 🔹 Explanation:
A **stored procedure** is a **precompiled SQL code** that can **execute multiple SQL statements**.

🔹 **Stored Procedure vs. Function**:

| Feature | Stored Procedure | Function |
|---------|----------------|----------|
| **Purpose** | Performs actions like `INSERT`, `UPDATE`, `DELETE` | Computes and returns a value |
| **Returns a Value?** | ❌ No (Can return multiple results via OUT params) | ✅ Yes (Always returns a single value) |
| **Allows Transactions?** | ✅ Yes | ❌ No |
| **Can Modify Data?** | ✅ Yes (`INSERT`, `UPDATE`, etc.) | ❌ No (Read-Only) |
| **Calling Syntax** | `CALL procedure_name();` | `SELECT function_name();` |

### 🔹 Example Stored Procedure:
```sql
DELIMITER //
CREATE PROCEDURE GetAccountBalance (IN acc_id INT)
BEGIN
    SELECT balance FROM accounts WHERE account_id = acc_id;
END //
DELIMITER ;
```

🔹 **Calling the Procedure**:
```sql
CALL GetAccountBalance(1);
```

---

## 62. How do you pass parameters to a stored procedure?

### 🔹 Explanation:
Stored procedures support **three types of parameters**:
1️⃣ **IN** – Input only  
2️⃣ **OUT** – Returns a value  
3️⃣ **INOUT** – Used for both input & output  

### 🔹 Example Query:

**Procedure with Parameters**
```sql
DELIMITER //
CREATE PROCEDURE TransferMoney (IN sender_id INT, IN receiver_id INT, IN amount DECIMAL(10,2))
BEGIN
    UPDATE accounts SET balance = balance - amount WHERE account_id = sender_id;
    UPDATE accounts SET balance = balance + amount WHERE account_id = receiver_id;
END //
DELIMITER ;
```

🔹 **Calling the Procedure**:
```sql
CALL TransferMoney(1, 2, 200.00);
```

---

## 63. Explain the difference between an IN parameter and an OUT parameter in stored procedures.

### 🔹 Explanation:

| Parameter Type | Purpose | Example |
|---------------|---------|---------|
| **IN** | Input only | `IN amount DECIMAL(10,2)` |
| **OUT** | Output only | `OUT balance DECIMAL(10,2)` |
| **INOUT** | Both input & output | `INOUT total DECIMAL(10,2)` |

### 🔹 Example Query:

1️⃣ **Using an OUT Parameter**
```sql
DELIMITER //
CREATE PROCEDURE GetBalance (IN acc_id INT, OUT acc_balance DECIMAL(10,2))
BEGIN
    SELECT balance INTO acc_balance FROM accounts WHERE account_id = acc_id;
END //
DELIMITER ;
```

2️⃣ **Calling the Procedure**
```sql
CALL GetBalance(1, @balance);
SELECT @balance; -- Output: 800.00
```

---

## 64. What are stored functions, and how do they differ from stored procedures?

### 🔹 Explanation:
A **stored function** returns a **single value** and is used within `SELECT` queries.

🔹 **Stored Procedure vs. Stored Function**:
- **Stored Procedures** perform **actions**.
- **Stored Functions** return **computed values**.

### 🔹 Example Query:

1️⃣ **Creating a Function to Get Account Balance**
```sql
DELIMITER //
CREATE FUNCTION GetBalance (acc_id INT) RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE bal DECIMAL(10,2);
    SELECT balance INTO bal FROM accounts WHERE account_id = acc_id;
    RETURN bal;
END //
DELIMITER ;
```

2️⃣ **Calling the Function**
```sql
SELECT GetBalance(1);
```

---

## 65. How do you call a stored function in a query?

### 🔹 Explanation:
A **stored function** is called **inside a SQL statement**.

### 🔹 Example Query:
```sql
SELECT GetBalance(1) AS balance;
```

🔹 **Output:**
| balance |
|---------|
| 800.00 |

---

## 66. Explain triggers in SQL and their use cases.

### 🔹 Explanation:
A **trigger** is a stored procedure that **automatically executes** when a **specific event** occurs.

🔹 **Use Cases:**
- **Audit Logs**: Track changes in tables.
- **Enforce Business Rules**: Prevent invalid data updates.
- **Automated Calculations**: Update totals when rows change.

### 🔹 Example Query:

1️⃣ **Creating a Trigger to Log Withdrawals**
```sql
DELIMITER //
CREATE TRIGGER after_withdrawal
AFTER UPDATE ON accounts
FOR EACH ROW
BEGIN
    IF OLD.balance > NEW.balance THEN
        INSERT INTO transactions (account_id, amount, transaction_type)
        VALUES (OLD.account_id, OLD.balance - NEW.balance, 'WITHDRAW');
    END IF;
END //
DELIMITER ;
```

2️⃣ **Trigger in Action**
```sql
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
SELECT * FROM transactions;
```

---

## 67. What is the difference between BEFORE and AFTER triggers?

| Trigger Type | Description | Example Use Case |
|-------------|-------------|------------------|
| **BEFORE** | Executes **before** the main operation | Validate balance before withdrawal |
| **AFTER** | Executes **after** the main operation | Log transaction after update |

### 🔹 Example:
```sql
DELIMITER //
CREATE TRIGGER before_withdrawal
BEFORE UPDATE ON accounts
FOR EACH ROW
BEGIN
    IF OLD.balance - NEW.balance < 0 THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Insufficient Funds';
    END IF;
END
//
DELIMITER ;
```

---

## 68. How do you handle errors in stored procedures?

### 🔹 Explanation:
Use **error handlers** in stored procedures to catch exceptions.

### 🔹 Example Query:

1️⃣ **Declaring an Error Handler**
```sql
DELIMITER //
CREATE PROCEDURE SafeTransfer (IN sender_id INT, IN receiver_id INT, IN amount DECIMAL(10,2))
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Transaction Failed';
    END;
    
    START TRANSACTION;
    UPDATE accounts SET balance = balance - amount WHERE account_id = sender_id;
    UPDATE accounts SET balance = balance + amount WHERE account_id = receiver_id;
    COMMIT;
END //
DELIMITER ;
```

---

## 69. Explain the concept of database normalization. What are the normal forms?



### 🔹 Explanation:  
**Normalization** is a process of organizing a database to **reduce redundancy** and **improve data integrity**. It ensures efficient storage and prevents anomalies during **inserts, updates, and deletes**.  

| **Normal Form** | **Description** |
|-------------|-------------|
| **1NF (First Normal Form)** | ✅ Each column must contain **atomic (indivisible) values**. <br> ✅ Each column must have **a single value per row** (no arrays or lists). <br> ✅ Each row must be **uniquely identifiable** (usually by a primary key). |
| **2NF (Second Normal Form)** | ✅ The table **must be in 1NF**. <br> ✅ Every **non-key column** must be **fully dependent** on the **entire** primary key. <br> ❌ **No partial dependencies** (where a column depends on part of a composite key). |
| **3NF (Third Normal Form)** | ✅ The table **must be in 2NF**. <br> ✅ **No transitive dependencies** (A → B → C should not exist). <br> ✅ Every **non-key column** must depend **only** on the primary key, not on another non-key column. |

🔹 **Example of Breaking Normal Forms:**  
- **1NF Violation:** A table stores multiple phone numbers in a single column.  
- **2NF Violation:** A table with a composite key where some columns depend only on part of the key.  
- **3NF Violation:** A table where a column like `city` depends on `zipcode` instead of the primary key.  

---

### 70. What is denormalization, and when should it be used? 

### 🔹 Explanation:  
**Denormalization** is the process of **reintroducing redundancy** in a database to **improve read performance** at the cost of increased storage. It reduces the number of **joins** required for queries, making reads faster.  

| **Aspect** | **Denormalization Explanation** |
|-------------|-------------|
| **Why Use It?** | ✅ **Faster query performance** (fewer joins). <br> ✅ **Better read performance** in large datasets. |
| **When to Use?** | ✅ **Data Warehouses** (OLAP systems with high read/query loads). <br> ✅ **Read-heavy applications** (e.g., dashboards, reporting). <br> ✅ **Precomputed aggregates** (e.g., storing total order amounts instead of calculating on the fly). |
| **Trade-Offs** | ❌ **Increased storage usage** due to redundant data. <br> ❌ **More complex updates** (must update all redundant copies). <br> ❌ **Risk of data inconsistency** if not properly managed. |


---
