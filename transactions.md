# SQL Interview Questions and Answers (Transactions & ACID Properties)

Let's use **a real-world scenario**:  

📌 **Banking System Example**  
We have:
- **Accounts Table**: Stores account balances.
- **Transactions Table**: Logs financial transactions.

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

## 44. What are ACID properties in a database?

### 🔹 Explanation:
ACID is an acronym for **Atomicity, Consistency, Isolation, and Durability**, which are the four key properties that ensure the reliability of database transactions.

| ACID Property | Description | Example |
|--------------|-------------|---------|
| **Atomicity** | Ensures **all parts** of a transaction are completed or **none at all** | If a fund transfer from Alice to Bob fails midway, no partial transaction occurs. |
| **Consistency** | Ensures database remains in a **valid state** before and after a transaction | If Alice sends $100 to Bob, Alice’s balance reduces and Bob’s balance increases by the same amount. |
| **Isolation** | Ensures **concurrent transactions** do not interfere | Multiple users transferring money should not interfere with each other’s transactions. |
| **Durability** | Ensures committed transactions are **permanently stored** | If power fails after a transaction is committed, it remains intact. |

### 🔹 Example Query:
Ensuring **Atomicity & Consistency**:
```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE account_id = 1; -- Alice sends $100
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2; -- Bob receives $100

COMMIT;
```
🔹 **If either statement fails, the transaction does not go through.**

---

## 45. How do you start, commit, and rollback a transaction in SQL?

### 🔹 Explanation:
- **START TRANSACTION**: Begins a transaction.
- **COMMIT**: Saves the transaction permanently.
- **ROLLBACK**: Undoes the transaction if something goes wrong.

### 🔹 Example Query:

1️⃣ **Starting a Transaction**:
```sql
START TRANSACTION;
```

2️⃣ **Performing Updates (Money Transfer Example)**:
```sql
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1; -- Alice deducts $100
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2; -- Bob gets $100
```

3️⃣ **Committing the Transaction** (Making Changes Permanent):
```sql
COMMIT;
```

4️⃣ **Rolling Back if an Error Occurs**:
```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 5000 WHERE account_id = 1; -- Invalid transaction (insufficient funds)
UPDATE accounts SET balance = balance + 5000 WHERE account_id = 2; -- Should not happen

ROLLBACK; -- Undo changes
```
🔹 **If Alice does not have $5000, `ROLLBACK` will revert all operations.**

---

## 46. Explain the difference between SAVEPOINT and ROLLBACK.

### 🔹 Explanation:

| Feature | Description | Example Use Case |
|---------|-------------|------------------|
| **ROLLBACK** | Reverts **the entire transaction** | If a transfer fails, undo everything. |
| **SAVEPOINT** | Creates a **checkpoint** to roll back **partially** | If an error happens midway, undo only part of the transaction. |

### 🔹 Example Queries:

1️⃣ **Using ROLLBACK** (Undoing everything):
```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;

ROLLBACK; -- Undo all changes
```

2️⃣ **Using SAVEPOINT (Undoing Partially)**:
```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
SAVEPOINT step1;

UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
SAVEPOINT step2;

ROLLBACK TO step1; -- Only undoes step2, keeping step1 changes
COMMIT;
```
🔹 **Use Case:**  
If a transaction has **multiple steps**, you can **rollback only a part** without losing everything.

---

## 47. What is transaction isolation, and what are the different levels?

### 🔹 Explanation:
**Transaction Isolation** defines how transactions interact when executed **simultaneously**.

| Isolation Level | Description | Prevents Issues? |
|----------------|-------------|------------------|
| **Read Uncommitted** | Allows reading uncommitted changes | ❌ Dirty Reads |
| **Read Committed** | Only reads committed transactions | ✅ Dirty Reads |
| **Repeatable Read** | Prevents changes to rows read by a transaction | ✅ Dirty & Non-Repeatable Reads |
| **Serializable** | Fully isolates transactions | ✅ Dirty, Non-Repeatable Reads & Phantom Reads |

### 🔹 Example Query:
Setting Isolation Level in MySQL:
```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
START TRANSACTION;
```

🔹 **Example Use Case:**  
- **Banking Transactions** should use `SERIALIZABLE` to prevent fraud.
- **E-commerce Inventory** may use `REPEATABLE READ` to allow stock updates while preventing inconsistencies.

---

## 48. Provide an example of a deadlock situation in SQL.

### 🔹 Explanation:
A **deadlock** occurs when **two transactions block each other** by holding locks that the other transaction needs.

🔹 **Example Scenario:**  
1️⃣ Alice is **transferring money** to Bob.  
2️⃣ Bob is **transferring money** to Alice **at the same time**.  
3️⃣ Both transactions lock each other out, resulting in a deadlock.

### 🔹 Example Deadlock Situation:
```sql
-- Transaction 1 (Alice transfers to Bob)
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1; -- Alice locks row 1
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2; -- Waits for row 2
COMMIT;

-- Transaction 2 (Bob transfers to Alice at the same time)
START TRANSACTION;
UPDATE accounts SET balance = balance - 50 WHERE account_id = 2; -- Bob locks row 2
UPDATE accounts SET balance = balance + 50 WHERE account_id = 1; -- Waits for row 1
COMMIT;
```

🔹 **What Happens?**
- Transaction 1 **locks Alice’s balance** and waits for Bob’s balance.
- Transaction 2 **locks Bob’s balance** and waits for Alice’s balance.
- **Neither transaction can proceed!**

### 🔹 Solution: Preventing Deadlocks
- **Use Ordered Locks**: Always lock **accounts in a consistent order**.
- **Set Timeout Limits**: Detect deadlocks and abort transactions automatically.
```sql
SET innodb_lock_wait_timeout = 5; -- Abort after 5 seconds
```

---

# 📌 Summary Table

| Concept | Explanation | Example Query |
|---------|------------|--------------|
| **ACID Properties** | Ensures reliable transactions | `START TRANSACTION; UPDATE ... COMMIT;` |
| **COMMIT & ROLLBACK** | Save or undo transactions | `ROLLBACK;` |
| **SAVEPOINT** | Partial rollback | `SAVEPOINT sp1; ROLLBACK TO sp1;` |
| **Isolation Levels** | Controls transaction interaction | `SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;` |
| **Deadlock** | Two transactions block each other | Avoid by using ordered locks |

---


