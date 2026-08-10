# SQL & DBMS Fundamentals

> Notes compiled from handwritten study material — Day 1
> Topics: SQL basics, DBMS vs RDBMS, Codd's Rules, DDL/DML/DQL/DCL/TCL, Oracle, Tables, Data Types, Constraints, Keys

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

## 4. Codd's 12 Rules (E.F. Codd)

> Rules that define what a **true relational database** must follow.

1. **What is the Codd Rule?** — Rules enforced in an RDBMS so that data entered in a table is *validated* before it goes into a DB table.
2. **According to E.F. Codd rules**, we can store single, atomic values in a cell — i.e., a cell must hold **a single value**.
3. **Informs**: stores the data in table form. It also considers "meta data" (data about data — column names, data types, etc.) as data — meta data must be stored/validated in the same way.
4. **Details of data & meta data**: meta data must be stored in the DB in two ways:
   - **Data Dictionary**
   - **System Catalog**

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

### 5.4 DCL — Data Control Language
Controls permissions and access to the database.

| Command | Purpose |
|---|---|
| `GRANT` | Give access/permission to a user |
| `REVOKE` | Take away access/permission |

### 5.5 TCL — Transaction Control Language
Used to manage transactions in a database.

| Command | Purpose |
|---|---|
| `COMMIT` | Save all changes made in the transaction |
| `ROLLBACK` | Undo changes made in the transaction |
| `SAVEPOINT` | Set a point within a transaction to roll back to |

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

## 8. Data Types

### 8.1 Numeric Data Types

| Data Type | Description |
|---|---|
| `INT` | Whole numbers |
| `NUMERIC(P, S)` | Decimal numbers — P = precision (total digits), S = scale (digits after decimal) |
| `SERIAL` | Auto-incrementing integer (commonly used for ID / Primary Key columns) |

**Typical uses:**
- `age`, `quantity` → whole numbers (`INT`)
- `price`, `weight` → decimal numbers (`NUMERIC`)
- `id (PK)` → `SERIAL`

### 8.2 Date/Time Data Types

| Data Type | Description |
|---|---|
| `DATE` | Stores date only → `YYYY-MM-DD` |
| `TIME` | Stores time only → `HH:MM:SS` |
| `TIMESTAMP` | Stores date **and** time → `YYYY-MM-DD HH:MM:SS` |

### 8.3 Character/String Data Types

| Data Type | Description |
|---|---|
| `CHAR(n)` | Fixed-length string |
| `VARCHAR(n)` | Chosen length string (max `n` chars, but variable, doesn't waste storage) — **commercially widely used** because storage adjusts to actual content length |
| `TEXT` | Variable length string, no maximum limit (or a very high max) |

**String length behavior — recap:**

| Type | Length behavior |
|---|---|
| Fixed-length string (`CHAR(n)`) | Always reserves exactly `n` characters of storage, even if the value is shorter |
| Varying-length string (`VARCHAR(n)`) | Reserves storage based on actual content, up to a maximum of `n` |
| No-maximum-length string (`TEXT`) | No practical upper limit — used for long free-form text (e.g., blog posts, descriptions) |

### 8.4 Boolean Data Type
- Stores **`TRUE`** or **`FALSE`** only.
- Use case example: `is_in_stock BOOLEAN` — a flag for whether an item is available.

### 8.5 ENUM Data Type
- A list of **fixed, predefined values** — a column using ENUM can only be set to one value **from that list**.

```sql
-- Example
gender ENUM('Male', 'Female', 'Other')
```

### 8.6 Binary / BLOB Data Type
- **Binary** data type stores raw `0`/`1` bit data.
- **BLOB (Binary Large OBject)** — used to store **large binary objects** such as images, audio, video, or files, directly inside the database.

```sql
-- Example
profile_pic BLOB
```

> **Rule of thumb:** use `VARCHAR`/`TEXT` for readable text, and `BLOB`/`BINARY` for raw file-like data.

---

## 9. Constraints

> **Constraints** are rules used to **check the values** entered in a column of a database table.

| Constraint | Description |
|---|---|
| `NOT NULL` | Column must have a value; NULL values are not allowed on insert |
| `UNIQUE` | Values in the column must not repeat across rows |
| `CHECK` | Boolean expression — accepts the value **only if** the condition evaluates to true (e.g., `age > 0`) |
| `DEFAULT` | If no value is provided, the column takes the default value |
| `PRIMARY KEY` | Uniquely identifies each row; combines `NOT NULL` + `UNIQUE` |
| `FOREIGN KEY` | Links a column to the Primary Key of another table |

### 9.1 Example: Table with constraints
```sql
CREATE TABLE customer (
    id       SERIAL PRIMARY KEY,
    f_name   VARCHAR(50) NOT NULL,
    email    VARCHAR(100) UNIQUE,
    age      INT CHECK (age > 0),
    country  VARCHAR(50) DEFAULT 'India'
);
```

> **Never/always rules:**
> - `NOT NULL` → the column **cannot** insert/have NULL values.
> - `UNIQUE` → column values **can't be duplicated**.
> - `CHECK` → accepts a value only if a boolean condition is satisfied (e.g., `age >= 18`).

### 9.2 Worked examples of each constraint

**`NOT NULL`** — every row *must* provide a value for this column; an `INSERT` without it will fail.
```sql
CREATE TABLE student (
    id    SERIAL PRIMARY KEY,
    name  VARCHAR(50) NOT NULL,   -- name is required
    email VARCHAR(100)            -- email is optional
);

-- This fails, because name is NULL:
INSERT INTO student (id, email) VALUES (1, 'a@x.com');
```

**`UNIQUE`** — no two rows can have the same value in this column.
```sql
CREATE TABLE student (
    id    SERIAL PRIMARY KEY,
    email VARCHAR(100) UNIQUE
);

INSERT INTO student (id, email) VALUES (1, 'aj@mail.com');
INSERT INTO student (id, email) VALUES (2, 'aj@mail.com'); -- ❌ fails: duplicate email
```

**`CHECK`** — the value must satisfy a boolean condition, or it's rejected.
```sql
CREATE TABLE student (
    id  SERIAL PRIMARY KEY,
    age INT CHECK (age >= 18)
);

INSERT INTO student (id, age) VALUES (1, 20);  -- ✅ passes (20 >= 18)
INSERT INTO student (id, age) VALUES (2, 15);  -- ❌ fails  (15 < 18)
```

**`DEFAULT`** — if a value isn't given, MySQL fills in the default automatically.
```sql
CREATE TABLE student (
    id      SERIAL PRIMARY KEY,
    country VARCHAR(50) DEFAULT 'India'
);

INSERT INTO student (id) VALUES (1);
-- country column is automatically set to 'India'
```

---

## 10. Primary Key & Foreign Key

### 10.1 Primary Key (PK)
- A column (or set of columns) with a **unique** value for each row.
- Uniquely identifies each record in a table.
- Cannot contain NULL values.

### 10.2 Foreign Key (FK)
- A column in one table that **refers to the Primary Key of another table**.
- Used to **link/relate** two tables.
- The "child" table holds the FK, and the FK values **belong to / come from** the "parent" table's PK column.
- FK values **must exist** as a PK value in the parent table (referential integrity) — you can't insert a value into the FK column that doesn't already exist as a PK in the parent table.
- **A single table can contain more than one foreign key** (referencing multiple different parent tables).

### 10.3 Worked example: `owner` (parent) and `pet` (child)

**`owner` table (Parent)**

| id (PK) | name |
|---|---|
| 1 | Dog |
| 2 | Cat |

**`pet` table (Child)**

| id (PK) | name | owner_id (FK → owner.id) |
|---|---|---|
| 1 | Rex | 1 |
| 2 | Whiskers | 2 |

```sql
-- Parent table
CREATE TABLE owner (
    id   SERIAL PRIMARY KEY,
    name VARCHAR(50)
);

-- Child table — owner_id is the Foreign Key
CREATE TABLE pet (
    id       SERIAL PRIMARY KEY,
    name     VARCHAR(50),
    owner_id INT,
    FOREIGN KEY (owner_id) REFERENCES owner(id)
);

-- ✅ Works — owner id 1 exists in the parent table
INSERT INTO pet (id, name, owner_id) VALUES (1, 'Rex', 1);

-- ❌ Fails — there is no owner with id = 99
INSERT INTO pet (id, name, owner_id) VALUES (2, 'Ghost', 99);
```

### 10.4 Static vs Dynamic constraint checks
- **Unique constraint** → checked against **only one column, within the same table**.
- **Foreign key constraint** → checked against a **different (parent) table's** column — a cross-table check, which is why it's used to enforce relationships between tables.

---



## 11. Quick Recap Table

| Concept | Key Point |
|---|---|
| SQL | Language to talk to relational databases |
| DBMS | Manages data + security + files |
| RDBMS | DBMS following the relational (table) model |
| Codd's Rules | Define what makes a DB "truly relational" |
| DDL | Structure: CREATE, ALTER, RENAME, TRUNCATE, DROP |
| DML | Data: INSERT, UPDATE, DELETE |
| DQL | Query: SELECT |
| DCL | Access: GRANT, REVOKE |
| TCL | Transactions: COMMIT, ROLLBACK, SAVEPOINT |
| Oracle | Secure + scalable RDBMS with PL/SQL, strong lock/deadlock handling |
| Data Types | INT, NUMERIC, VARCHAR, CHAR, TEXT, DATE, TIME, TIMESTAMP, BOOLEAN, ENUM, SERIAL, BLOB/BINARY |
| Constraints | NOT NULL, UNIQUE, CHECK, DEFAULT, PK, FK |

---

*Next up: Deeper dive into DML/CRUD operations (`INSERT`, `SELECT`, `UPDATE`, `DELETE`) with full syntax examples, and JOINs.*
