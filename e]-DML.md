# DML — Data Manipulation Language

> Notes compiled from handwritten study material
> Topics: INSERT, UPDATE, DELETE — with full syntax and examples

---

## 1. What is DML?

**DML (Data Manipulation Language)** is the part of SQL used to **manipulate the data inside** a table — adding, changing, or removing rows.

> DML deals with the **data/records** — not the table's structure. That's what DDL is for.

| Command | Purpose |
|---|---|
| `INSERT` | Add new row(s) into a table |
| `UPDATE` | Modify existing row(s) |
| `DELETE` | Remove existing row(s) |

> ⚠️ **Important:** Unlike DDL, DML statements are **not auto-committed** — they run inside a transaction and can be undone with `ROLLBACK` (until `COMMIT` is run). See the TCL notes for more.

---

## 2. INSERT

Used to **add new row(s)** into a table.

### 2.1 Insert a single row (all columns)
```sql
INSERT INTO student VALUES (1, 'AJ', 22, 'aj@mail.com', 'India');
```
> Values must be given **in the same order** as the columns were defined when the table was created.

### 2.2 Insert a single row (specific columns)
```sql
INSERT INTO student (id, name, age) VALUES (1, 'AJ', 22);
```
> Any column not listed will get its `DEFAULT` value, or `NULL` if no default exists (and it's not `NOT NULL`).

### 2.3 Insert multiple rows at once
```sql
INSERT INTO student (id, name, age) VALUES
    (1, 'AJ', 22),
    (2, 'Puneeth', 23),
    (3, 'Ravi', 21);
```

### 2.4 Insert data copied from another table
```sql
INSERT INTO student_backup (id, name, age)
SELECT id, name, age FROM student WHERE age > 20;
```

---

## 3. UPDATE

Used to **modify existing row(s)** in a table.

### 3.1 Update a single column
```sql
UPDATE student SET age = 23 WHERE id = 1;
```

### 3.2 Update multiple columns at once
```sql
UPDATE student
SET age = 23, country = 'USA'
WHERE id = 1;
```

### 3.3 Update multiple rows using a condition
```sql
UPDATE student SET country = 'India' WHERE country IS NULL;
```

> ⚠️ **Always use a `WHERE` clause with `UPDATE`.** Without one, **every row** in the table gets updated.
```sql
-- ❌ Dangerous — updates ALL rows in the table
UPDATE student SET country = 'India';
```

---

## 4. DELETE

Used to **remove existing row(s)** from a table.

### 4.1 Delete specific row(s)
```sql
DELETE FROM student WHERE id = 1;
```

### 4.2 Delete rows matching a condition
```sql
DELETE FROM student WHERE age < 18;
```

### 4.3 Delete all rows (structure stays)
```sql
DELETE FROM student;
```

> ⚠️ **Always use a `WHERE` clause with `DELETE`.** Without one, **every row** in the table is removed (though the empty table itself still exists — unlike `DROP`).

---

## 5. INSERT vs UPDATE vs DELETE — Comparison

| | `INSERT` | `UPDATE` | `DELETE` |
|---|---|---|---|
| Type | DML | DML | DML |
| Action | Adds new row(s) | Modifies existing row(s) | Removes row(s) |
| Needs `WHERE`? | N/A | Recommended (else all rows updated) | Recommended (else all rows deleted) |
| Affects table structure? | ❌ No | ❌ No | ❌ No |
| Rollback possible? | ✅ Yes (inside a transaction) | ✅ Yes | ✅ Yes |

---

## 6. DELETE vs TRUNCATE — Quick Reminder

Since these two are often confused (`DELETE` is DML, `TRUNCATE` is DDL — see the DDL notes for full comparison):

| | `DELETE` | `TRUNCATE` |
|---|---|---|
| Type | DML | DDL |
| Can use `WHERE`? | ✅ Yes | ❌ No |
| Rollback possible? | ✅ Yes | ❌ No (auto-committed) |
| Resets AUTO_INCREMENT? | ❌ No | ✅ Yes |

---

## 7. Common Clauses Used with DML

| Clause | Purpose | Example |
|---|---|---|
| `WHERE` | Filters which rows are affected | `WHERE age > 18` |
| `ORDER BY` | Sorts rows (mainly used with `DELETE ... LIMIT`) | `ORDER BY age ASC` |
| `LIMIT` | Restricts number of rows affected | `LIMIT 5` |

```sql
-- Delete the 5 oldest student records
DELETE FROM student ORDER BY age DESC LIMIT 5;
```

---

## 8. Quick Recap

| Command | What it does |
|---|---|
| `INSERT` | Adds new row(s) to a table |
| `UPDATE` | Changes values in existing row(s) |
| `DELETE` | Removes row(s) from a table |
| Golden rule | Always pair `UPDATE`/`DELETE` with a `WHERE` clause unless you *really* mean to affect every row |

---
📌 **Related notes:** [← Data Definition Language](d%5D-Data%20Definition%20Language.md) | [Structured Query Language](b%5D-Structured%20Query%20Language.md) | [Back to README](README.md)
