

# SQL Interview Questions and Answers (Basics of SQL)

## 1. What is SQL? Explain its importance.

### 🔹 Explanation:
SQL (Structured Query Language) is a standardized programming language used to **create, retrieve, update, and delete** data in relational databases. It serves as the foundation for managing structured data across various database management systems (DBMS) like **MySQL, PostgreSQL, Oracle, SQL Server, and SQLite**.

### 🔹 Importance:
- **Data Management:** Enables efficient interaction with relational databases.
- **Standardization:** SQL follows ANSI and ISO standards, ensuring compatibility across different DBMS.
- **Powerful Queries:** Supports complex queries using joins, subqueries, and aggregation.
- **Data Integrity:** Provides constraints (e.g., PRIMARY KEY, FOREIGN KEY) to enforce rules.
- **Security:** Offers authentication and access control using roles and privileges.

### 🔹 Example:
Retrieving all employees from a database:

```sql
SELECT * FROM employees;
```

---

## 2. What are the differences between SQL and NoSQL?

### 🔹 Explanation:
SQL and NoSQL are two types of database technologies used for different use cases.

| Feature  | SQL (Relational DB) | NoSQL (Non-Relational DB) |
|----------|--------------------|---------------------------|
| **Structure** | Structured (Tables, Rows, Columns) | Flexible (Document, Key-Value, Graph, Column-Family) |
| **Schema** | Fixed schema (predefined structure) | Dynamic schema (flexible, can store unstructured data) |
| **Scalability** | Vertical Scaling (Adding CPU, RAM) | Horizontal Scaling (Adding more nodes) |
| **Best For** | ACID transactions, structured data (e.g., Banking, ERP) | Big Data, real-time apps, unstructured data (e.g., Social Media, IoT) |
| **Examples** | MySQL, PostgreSQL, SQL Server, Oracle | MongoDB, Cassandra, Firebase, Redis |

### 🔹 Example:
SQL Database Table:
```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  email VARCHAR(100)
);
```

NoSQL (MongoDB - Document Store):
```json
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "name": "Alice",
  "email": "alice@example.com"
}
```

---

## 3. Explain the types of SQL commands (DDL, DML, DCL, TCL).

SQL is categorized into **four main types** of commands:

| Type | Description | Example Commands |
|------|-------------|------------------|
| **DDL (Data Definition Language)** | Defines structure/schema of the database. | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` |
| **DML (Data Manipulation Language)** | Handles CRUD operations (Create, Read, Update, Delete). | `INSERT`, `UPDATE`, `DELETE`, `SELECT` |
| **DCL (Data Control Language)** | Manages user access and permissions. | `GRANT`, `REVOKE` |
| **TCL (Transaction Control Language)** | Controls transactions. | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

### 🔹 Example:
Creating a table (DDL):
```sql
CREATE TABLE students (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  age INT
);
```

Inserting data (DML):
```sql
INSERT INTO students (id, name, age) VALUES (1, 'John Doe', 22);
```

---

## 4. What is a database? How is it different from a table?

### 🔹 Explanation:
A **database** is a structured collection of data that allows efficient retrieval, insertion, updating, and deletion of information.

A **table** is a structured format within a database where data is stored in **rows (records) and columns (fields)**.

### 🔹 Differences:

| Feature  | Database | Table |
|----------|---------|-------|
| **Definition** | A collection of structured data | A specific dataset within a database |
| **Contains** | Multiple tables | Multiple rows and columns |
| **Example** | `employees_db` (Database) | `employees` (Table inside the database) |

### 🔹 Example:
Creating a database and a table:
```sql
CREATE DATABASE company;
USE company;

CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  department VARCHAR(50)
);
```

---

## 5. What are the primary data types in SQL (e.g., INT, VARCHAR, DATE)?

### 🔹 Explanation:
SQL supports different **data types** based on the type of data stored.

| Data Type | Description | Example |
|-----------|-------------|---------|
| **INT** | Stores whole numbers | `age INT` (23) |
| **VARCHAR(N)** | Stores variable-length text | `name VARCHAR(50)` ('Alice') |
| **CHAR(N)** | Stores fixed-length text | `country CHAR(3)` ('USA') |
| **DATE** | Stores date values | `birth_date DATE` ('1995-10-15') |
| **DECIMAL(M,N)** | Stores fixed-point numbers | `salary DECIMAL(10,2)` (5000.50) |

---

## 6. What is the difference between CHAR and VARCHAR?

### 🔹 Explanation:
Both `CHAR` and `VARCHAR` store text, but they differ in **storage and performance**.

| Feature | CHAR | VARCHAR |
|---------|------|---------|
| **Storage** | Fixed-length | Variable-length |
| **Performance** | Faster (fixed allocation) | Slightly slower (dynamic allocation) |
| **Best For** | Short, fixed-length fields (e.g., Country Codes) | Long, dynamic fields (e.g., Names, Emails) |

### 🔹 Example:
```sql
CREATE TABLE test_table (
  code CHAR(5), 
  name VARCHAR(50)
);
```

If `code = 'USA'`, `CHAR(5)` stores **'USA  '** (padded spaces), while `VARCHAR(50)` stores **'USA'** without padding.

---

## 7. How do you retrieve all records from a table using a SELECT statement?

### 🔹 Explanation:
The `SELECT` statement is used to fetch data from a table.

### 🔹 Syntax:
```sql
SELECT * FROM table_name;
```

### 🔹 Example:
Retrieving all employees:
```sql
SELECT * FROM employees;
```

---

## 8. How do you filter records using the WHERE clause?

### 🔹 Explanation:
The `WHERE` clause filters records based on a condition.

### 🔹 Example:
```sql
SELECT * FROM employees WHERE department = 'IT';
```

---

## 9. What is the use of logical operators (AND, OR, NOT)? Provide examples.

### 🔹 Explanation:
Logical operators **combine multiple conditions** in a `WHERE` clause.

| Operator | Description |
|----------|-------------|
| **AND** | Returns records where all conditions are `TRUE` |
| **OR** | Returns records where at least one condition is `TRUE` |
| **NOT** | Returns records where the condition is `FALSE` |

### 🔹 Example:
```sql
SELECT * FROM employees WHERE department = 'IT' AND age > 25;
```

---

## 10. What is the difference between NULL and an empty string?

### 🔹 Explanation:
- **NULL:** Represents the absence of a value.
- **Empty String (`''`):** A string with zero length.

### 🔹 Example:
```sql
SELECT * FROM users WHERE email IS NULL;  -- Finds users without an email
SELECT * FROM users WHERE email = '';    -- Finds users with an empty email
```

---


