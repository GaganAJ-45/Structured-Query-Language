# SQL & DBMS Fundamentals

> Notes compiled from handwritten study material — Day 1
> Topics: SQL basics, DDL/DML/DQL/DCL/TCL, Oracle, Tables, Data Types, Constraints, Keys

---

## 1. What is SQL?

**SQL (Structured Query Language)** is the language used to interact with a relational database.

It is used to:
- Create tables
- Insert data
- Modify/update data
- Query (retrieve) data
- Much more (manage users, permissions, transactions, etc.)

> SQL syntax is *similar* across different database systems (MySQL, PostgreSQL, Oracle, SQL Server), but each has its own small variations — often called "dialects."

Examples of RDBMS software that use SQL: **MySQL, Oracle, PostgreSQL, SQL Server**.

---
## 2. Core Concepts: Data, Object, Properties

| Term | Meaning |
|---|---|
| **Data** | Raw facts — "nothing but the properties of an object/entity." |
| **Object / Entity** | A real-world thing we store data about (e.g., a person). |
| **Properties / Attributes** | The characteristics of that object. |

**Example — Object: `AJ`**

| Property | Value |
|---|---|
| name | AJ |
| age | 22 |
| DOB | 22-05-2004 |

> Properties can be **static** (fixed, e.g., DOB) or **dynamic** (changeable, e.g., age).

---

> **Relational Model**: Logically represents data as tables. RDBMS software follows this model.
---
> Data types and constraints have been moved to a dedicated file — see [Data Types and Constraints](c%5D-Data%20types%20and%20cons.md).
---

## 5. SQL Sub-Languages

SQL is divided into categories based on what kind of operation they perform:


| Category | Full Form | Purpose |
|---|---|---|
| **DDL** | Data Definition Language | Defines/modifies structure of DB objects (tables) |
| **DML** | Data Manipulation Language | Insert, update, delete data |
| **DQL** | Data Query Language | Retrieve/query data |
| **DCL** | Data Control Language | Controls access/permissions |
| **TCL** | Transaction Control Language | Manages transactions |

### 5.1 DDL — Data Definition Language
Used to define/change the structure of existing tables in a database.

| Command | Purpose |
|---|---|
| `CREATE` | Create a new table |
| `RENAME` | Rename a table |
| `ALTER` | Modify table structure (add/remove/modify column) |
| `TRUNCATE` | Remove all rows, keep structure |
| `DROP` | Delete a table entirely |

**Syntax patterns:**
```sql
-- Alter table: modify column data type
ALTER TABLE table_name MODIFY column_name new_data_type;

-- Alter table: modify column name
ALTER TABLE table_name RENAME COLUMN old_name TO new_name;
```
---
> @ For More Info - [[d]DDL](d%5D-DDL.md)
---

### 5.2 DML — Data Manipulation Language
Used to manipulate (add/change/remove) the data **inside** a table.

| Command | Purpose |
|---|---|
| `INSERT` | Add new row(s) |
| `UPDATE` | Modify existing data |
| `DELETE` | Remove existing row(s) |

```sql
-- INSERT
INSERT INTO student (id, name, age) VALUES (1, 'AJ', 22);

-- UPDATE
UPDATE student SET age = 23 WHERE id = 1;

-- DELETE
DELETE FROM student WHERE id = 1;
```
---
> @ For More Info - [[e]DML](e%5D-DML.md)
---
### 5.3 DQL — Data Query Language
Used purely to **retrieve/read** data from a table — doesn't change any data. Some textbooks group `SELECT` under DML, but it's cleaner to treat it as its own category since it *only reads*, never *writes*.

| Command | Purpose |
|---|---|
| `SELECT` | Retrieve/query data from one or more tables |

```sql
-- Basic SELECT
SELECT * FROM student;

-- SELECT with filter
SELECT name, age FROM student WHERE age > 18;

-- SELECT with sorting
SELECT * FROM student ORDER BY age DESC;
```
---
> @ For More Info - [[f]DQL](e%5D-DQL.md)
---

### 5.4 DCL — Data Control Language
Controls permissions and access to the database.

| Command | Purpose |
|---|---|
| `GRANT` | Give access/permission to a user |
| `REVOKE` | Take away access/permission |
---
> @ For More Info - - [[g]DCL](g%5D-DCL.md)
---

### 5.5 TCL — Transaction Control Language
Used to manage transactions in a database.

| Command | Purpose |
|---|---|
| `COMMIT` | Save all changes made in the transaction |
| `ROLLBACK` | Undo changes made in the transaction |
| `SAVEPOINT` | Set a point within a transaction to roll back to |
---
> @ For More Info - - [[f]TCL](f%5D-TCL.md)
---

## 6. Oracle Database — Quick Overview

Oracle is one of the most widely used RDBMS software in the industry. Key points:

- **Highly secure** — strong built-in security and access-control features, suited to enterprise use.
- **Scalable** — can handle very large volumes of data without performance breakdown.
- **Powerful coding support** — comes with **PL/SQL** (Procedural Language/SQL), which lets you write procedures, functions, and triggers, not just plain queries.
- **Rich official documentation** — Oracle has extensive official docs/support, so individual developers can self-support and troubleshoot without needing a company-provided support contract.
- **Transaction handling** — strong support for **locks** and **deadlock** detection/handling, which matters a lot when many users access the same data at once.
- **Cross-language support** — works well with many programming languages (Java, Python, .NET, etc.) for connecting applications to the database.
- **Market position** — Oracle competes with other major databases like **AWS (Aurora/RDS), IBM Db2, MongoDB, SQL Server**, and is generally ranked among the top RDBMS platforms.

> **In short:** Oracle = strong security + scalability + PL/SQL programmability + strong transaction control. This makes it a common choice for large enterprise systems, while MySQL/PostgreSQL are more common for smaller-to-mid scale and open-source projects.

---

## 7. Database Tables

- A table contains **columns** and **rows** (records).
- Each **column** is defined with a **data type**, which defines what type of value can go into the column.
- Each column can be set to accept **only unique data** (no duplicates), or to **allow only one value per row**.
- In a relational DB, two tables can be **linked together** through **Primary Keys** and **Foreign Keys** (see Section 10).

---
