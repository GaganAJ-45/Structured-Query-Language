# SQL — Data Types & Constraints

> Notes compiled from handwritten study material
> Topics: Numeric, Date/Time, String, Boolean, ENUM, Binary/BLOB data types + NOT NULL, UNIQUE, CHECK, DEFAULT, PK, FK constraints

---

## 1. Data Types

### 1.1 Numeric Data Types

| Data Type | Description |
|---|---|
| `INT` | Whole numbers |
| `NUMERIC(P, S)` | Decimal numbers — P = precision (total digits), S = scale (digits after decimal) |
| `SERIAL` | Auto-incrementing integer (commonly used for ID / Primary Key columns) |

**Typical uses:**
- `age`, `quantity` → whole numbers (`INT`)
- `price`, `weight` → decimal numbers (`NUMERIC`)
- `id (PK)` → `SERIAL`

```sql
CREATE TABLE product (
    id     SERIAL PRIMARY KEY,
    qty    INT,
    price  NUMERIC(10, 2)   -- up to 10 digits total, 2 after the decimal
);
```

### 1.2 Date/Time Data Types

| Data Type | Description |
|---|---|
| `DATE` | Stores date only → `YYYY-MM-DD` |
| `TIME` | Stores time only → `HH:MM:SS` |
| `TIMESTAMP` | Stores date **and** time → `YYYY-MM-DD HH:MM:SS` |

```sql
CREATE TABLE event (
    id         SERIAL PRIMARY KEY,
    event_date DATE,
    start_time TIME,
    created_at TIMESTAMP
);

INSERT INTO event (id, event_date, start_time, created_at)
VALUES (1, '2026-08-10', '14:30:00', '2026-08-10 14:30:00');
```

### 1.3 Character/String Data Types

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

```sql
CREATE TABLE student (
    id       SERIAL PRIMARY KEY,
    code     CHAR(6),          -- e.g. 'AJ0001' — always 6 chars
    f_name   VARCHAR(50),       -- e.g. 'AJ' — only uses as much space as needed
    bio      TEXT               -- long free-form description
);
```

### 1.4 Boolean Data Type
- Stores **`TRUE`** or **`FALSE`** only.
- Use case example: `is_in_stock BOOLEAN` — a flag for whether an item is available.

```sql
CREATE TABLE product (
    id          SERIAL PRIMARY KEY,
    is_in_stock BOOLEAN DEFAULT TRUE
);
```

### 1.5 ENUM Data Type
- A list of **fixed, predefined values** — a column using ENUM can only be set to one value **from that list**.

```sql
CREATE TABLE student (
    id     SERIAL PRIMARY KEY,
    gender ENUM('Male', 'Female', 'Other')
);

INSERT INTO student (id, gender) VALUES (1, 'Male');   -- ✅ works
INSERT INTO student (id, gender) VALUES (2, 'Robot');  -- ❌ fails — not in the list
```

### 1.6 Binary / BLOB Data Type
- **Binary** data type stores raw `0`/`1` bit data.
- **BLOB (Binary Large OBject)** — used to store **large binary objects** such as images, audio, video, or files, directly inside the database.

```sql
CREATE TABLE profile (
    id          SERIAL PRIMARY KEY,
    profile_pic BLOB
);
```

> **Rule of thumb:** use `VARCHAR`/`TEXT` for readable text, and `BLOB`/`BINARY` for raw file-like data.

---

## 2. Constraints

> **Constraints** are rules used to **check the values** entered in a column of a database table.

| Constraint | Description |
|---|---|
| `NOT NULL` | Column must have a value; NULL values are not allowed on insert |
| `UNIQUE` | Values in the column must not repeat across rows |
| `CHECK` | Boolean expression — accepts the value **only if** the condition evaluates to true (e.g., `age > 0`) |
| `DEFAULT` | If no value is provided, the column takes the default value |
| `PRIMARY KEY` | Uniquely identifies each row; combines `NOT NULL` + `UNIQUE` |
| `FOREIGN KEY` | Links a column to the Primary Key of another table |

### 2.1 Example: Table with all constraints together
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

### 2.2 Worked examples of each constraint

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

### 2.3 Primary Key (PK)
- A column (or set of columns) with a **unique** value for each row.
- Uniquely identifies each record in a table.
- Cannot contain NULL values.

```sql
CREATE TABLE student (
    id SERIAL PRIMARY KEY,   -- guaranteed unique & never NULL
    name VARCHAR(50)
);
```

### 2.4 Foreign Key (FK)
- A column in one table that **refers to the Primary Key of another table**.
- Used to **link/relate** two tables.
- The "child" table holds the FK, and the FK values **belong to / come from** the "parent" table's PK column.
- FK values **must exist** as a PK value in the parent table (referential integrity) — you can't insert a value into the FK column that doesn't already exist as a PK in the parent table.
- **A single table can contain more than one foreign key** (referencing multiple different parent tables).

### 2.5 Worked example: `owner` (parent) and `pet` (child)

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

### 2.6 Static vs Dynamic constraint checks
- **Unique constraint** → checked against **only one column, within the same table**.
- **Foreign key constraint** → checked against a **different (parent) table's** column — a cross-table check, which is why it's used to enforce relationships between tables.

---

## 3. Quick Recap

| Concept | Key Point |
|---|---|
| `INT` / `NUMERIC` / `SERIAL` | Whole numbers, decimals, auto-increment IDs |
| `DATE` / `TIME` / `TIMESTAMP` | Date only, time only, date + time |
| `CHAR` / `VARCHAR` / `TEXT` | Fixed length, variable up-to-limit, unlimited long text |
| `BOOLEAN` | TRUE / FALSE flag |
| `ENUM` | One value from a fixed, predefined list |
| `BLOB` / `BINARY` | Raw binary data, large files |
| `NOT NULL` | Value required |
| `UNIQUE` | No duplicate values allowed |
| `CHECK` | Value must satisfy a condition |
| `DEFAULT` | Auto-fills a value if none given |
| `PRIMARY KEY` | Unique + NOT NULL identifier for each row |
| `FOREIGN KEY` | Links a child table's column to a parent table's Primary Key |

---

*Next up: DML/CRUD operations (`INSERT`, `SELECT`, `UPDATE`, `DELETE`) with full syntax examples, and JOINs.*
