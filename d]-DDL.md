# DDL — Data Definition Language

> Notes compiled from handwritten study material
> Topics: CREATE, ALTER, RENAME, TRUNCATE, DROP — with full syntax and examples

---

## 1. What is DDL?

**DDL (Data Definition Language)** is the part of SQL used to **define, modify, and remove the structure** of database objects — mainly **tables**, but also databases, indexes, and views.

> DDL deals with the **structure/schema** (columns, data types, constraints) — not the actual row data inside the table. That's what DML/DQL are for.

| Command | Purpose |
|---|---|
| `CREATE` | Create a new table (or database) |
| `ALTER` | Modify an existing table's structure |
| `RENAME` | Rename a table |
| `TRUNCATE` | Remove all rows from a table, but keep the structure |
| `DROP` | Delete a table (or database) entirely, structure included |

> ⚠️ **Important:** DDL commands are **auto-committed** in MySQL — meaning once you run them, the change is permanent and generally **can't be rolled back** with `ROLLBACK` (unlike DML statements inside a transaction).

---

## 2. CREATE

Used to create a **new table** (or a new database).

### 2.1 Create a database
```sql
CREATE DATABASE school;
USE school;
```

### 2.2 Create a table
```sql
CREATE TABLE student (
    id      SERIAL PRIMARY KEY,
    name    VARCHAR(50) NOT NULL,
    age     INT CHECK (age > 0),
    email   VARCHAR(100) UNIQUE,
    country VARCHAR(50) DEFAULT 'India'
);
```

### 2.3 Create a table from another table (copy structure + data)
```sql
CREATE TABLE student_backup AS
SELECT * FROM student;
```

---

## 3. ALTER

Used to **modify the structure** of an existing table — add, remove, or change columns, without losing existing data.

### 3.1 Add a new column
```sql
ALTER TABLE student ADD COLUMN phone VARCHAR(15);
```

### 3.2 Modify a column's data type
```sql
ALTER TABLE student MODIFY COLUMN age SMALLINT;
```

### 3.3 Rename a column
```sql
ALTER TABLE student RENAME COLUMN name TO full_name;
```

### 3.4 Drop (remove) a column
```sql
ALTER TABLE student DROP COLUMN phone;
```

### 3.5 Add a constraint to an existing column
```sql
ALTER TABLE student ADD CONSTRAINT chk_age CHECK (age >= 18);
```

### 3.6 Add a Foreign Key to an existing table
```sql
ALTER TABLE student
ADD CONSTRAINT fk_school
FOREIGN KEY (school_id) REFERENCES school(id);
```

> `ALTER` is the most-used DDL command day-to-day because table requirements change often as an app grows — new fields, renamed fields, tightened constraints, etc.

---

## 4. RENAME

Used to rename an existing table.

```sql
-- Standard syntax
ALTER TABLE student RENAME TO learners;

-- MySQL also supports a direct RENAME TABLE statement
RENAME TABLE student TO learners;
```

> You can also rename **multiple tables in one statement** with `RENAME TABLE`:
```sql
RENAME TABLE student TO learners, teacher TO staff;
```

---

## 5. TRUNCATE

Removes **all rows** from a table, but **keeps the table structure** (columns, constraints, indexes) intact — like emptying the table but keeping the container.

```sql
TRUNCATE TABLE student;
```

**Key behaviors:**
- Deletes **all data**, no `WHERE` clause allowed (unlike `DELETE`).
- **Resets** `AUTO_INCREMENT` / `SERIAL` counters back to the starting value.
- Much **faster** than `DELETE` for large tables, because it doesn't log individual row deletions.
- Cannot be rolled back (it's a DDL operation, auto-committed).

---

## 6. DROP

Deletes a table (or database) **completely** — structure, data, indexes, constraints, everything. It's irreversible.

```sql
-- Drop a table entirely
DROP TABLE student;

-- Drop a table only if it exists (avoids an error if it doesn't)
DROP TABLE IF EXISTS student;

-- Drop an entire database
DROP DATABASE school;
```

---

## 7. TRUNCATE vs DELETE vs DROP — Comparison

| | `TRUNCATE` | `DELETE` | `DROP` |
|---|---|---|---|
| Type | DDL | DML | DDL |
| Removes | All rows | Rows (with optional `WHERE`) | Entire table/structure |
| Keeps table structure? | ✅ Yes | ✅ Yes | ❌ No |
| Can use `WHERE`? | ❌ No | ✅ Yes | ❌ No |
| Resets AUTO_INCREMENT? | ✅ Yes | ❌ No | N/A (table gone) |
| Rollback possible? | ❌ No (auto-committed) | ✅ Yes (inside a transaction) | ❌ No (auto-committed) |
| Speed | Fast | Slower (row-by-row) | Fast |

---

## 8. Quick Recap

| Command | What it does |
|---|---|
| `CREATE` | Makes a new table/database |
| `ALTER` | Adds/removes/modifies columns & constraints on an existing table |
| `RENAME` | Renames a table |
| `TRUNCATE` | Empties a table completely, keeps structure |
| `DROP` | Deletes a table/database entirely |

---
📌 **Related notes:** [← Structured Query Language](b%5D-Structured%20Query%20Language.md) | [Data Types and Constraints →](c%5D-Data%20types%20and%20cons.md) | [Back to README](README.md)
