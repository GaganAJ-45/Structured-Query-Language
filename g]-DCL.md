# DCL — Data Control Language

> Notes compiled from handwritten study material
> Topics: GRANT, REVOKE, Users & Privileges — with full syntax and examples

---

## 1. What is DCL?

**DCL (Data Control Language)** is the part of SQL used to **control access** to the database — deciding **who** (which user) is allowed to do **what** (which operations) on **which** database objects.

| Command | Purpose |
|---|---|
| `GRANT` | Give a user specific permissions/privileges |
| `REVOKE` | Take away permissions/privileges from a user |

> DCL is closely tied to **security** — it's how a DBMS enforces that only authorized users can read, modify, or manage certain data (see the DBMS security notes).

---

## 2. Users & Privileges — Basics

Before granting/revoking anything, a **user account** must exist. A user in MySQL is identified by `'username'@'host'` (the host controls *where* they can connect from).

### 2.1 Create a user
```sql
CREATE USER 'aj'@'localhost' IDENTIFIED BY 'strong_password';
```

### 2.2 View a user's current privileges
```sql
SHOW GRANTS FOR 'aj'@'localhost';
```

### 2.3 Delete a user
```sql
DROP USER 'aj'@'localhost';
```

---

## 3. GRANT

Used to **give** a user permission to perform specific operations on a database or table.

### 3.1 Grant a single privilege
```sql
GRANT SELECT ON school.student TO 'aj'@'localhost';
```
> `aj` can now only **read** (`SELECT`) from the `student` table — nothing else.

### 3.2 Grant multiple privileges at once
```sql
GRANT SELECT, INSERT, UPDATE ON school.student TO 'aj'@'localhost';
```

### 3.3 Grant all privileges on a table
```sql
GRANT ALL PRIVILEGES ON school.student TO 'aj'@'localhost';
```

### 3.4 Grant privileges on an entire database
```sql
GRANT ALL PRIVILEGES ON school.* TO 'aj'@'localhost';
```

### 3.5 Grant privileges on all databases (admin-level access)
```sql
GRANT ALL PRIVILEGES ON *.* TO 'admin_user'@'localhost';
```

### 3.6 Grant privilege *with* the ability to grant it to others
```sql
GRANT SELECT ON school.student TO 'aj'@'localhost' WITH GRANT OPTION;
```
> `WITH GRANT OPTION` lets `aj` pass on the `SELECT` privilege to **other** users too.

After any `GRANT`/`REVOKE`, apply changes immediately with:
```sql
FLUSH PRIVILEGES;
```

---

## 4. REVOKE

Used to **take away** a privilege that was previously granted to a user.

### 4.1 Revoke a single privilege
```sql
REVOKE INSERT ON school.student FROM 'aj'@'localhost';
```

### 4.2 Revoke multiple privileges
```sql
REVOKE SELECT, INSERT, UPDATE ON school.student FROM 'aj'@'localhost';
```

### 4.3 Revoke all privileges
```sql
REVOKE ALL PRIVILEGES ON school.student FROM 'aj'@'localhost';
```

### 4.4 Revoke the "grant to others" ability
```sql
REVOKE GRANT OPTION ON school.student FROM 'aj'@'localhost';
```

---

## 5. Common Privilege Types

| Privilege | Allows the user to... |
|---|---|
| `SELECT` | Read/query data |
| `INSERT` | Add new rows |
| `UPDATE` | Modify existing rows |
| `DELETE` | Remove rows |
| `CREATE` | Create new tables/databases |
| `DROP` | Delete tables/databases |
| `ALTER` | Modify table structure |
| `ALL PRIVILEGES` | Every privilege available |

---

## 6. Worked Example — Read-only vs Full-access User

```sql
-- Create two users
CREATE USER 'reader'@'localhost' IDENTIFIED BY 'pass1';
CREATE USER 'editor'@'localhost' IDENTIFIED BY 'pass2';

-- reader can only look at data
GRANT SELECT ON school.* TO 'reader'@'localhost';

-- editor can read, add, and update data — but not delete or change structure
GRANT SELECT, INSERT, UPDATE ON school.* TO 'editor'@'localhost';

FLUSH PRIVILEGES;
```

Later, if `editor` should no longer be able to insert new rows:
```sql
REVOKE INSERT ON school.* FROM 'editor'@'localhost';
FLUSH PRIVILEGES;
```

---

## 7. GRANT vs REVOKE — Comparison

| | `GRANT` | `REVOKE` |
|---|---|---|
| Action | Gives a privilege | Removes a privilege |
| Scope | Table, database, or all databases | Same as GRANT |
| Needs a user to exist? | ✅ Yes | ✅ Yes |
| Reversible? | ✅ Yes, with `REVOKE` | ✅ Yes, with `GRANT` again |
| Related option | `WITH GRANT OPTION` (allow re-granting) | `GRANT OPTION` can itself be revoked |

---

## 8. Quick Recap

| Command | What it does |
|---|---|
| `CREATE USER` | Creates a new database user account |
| `GRANT` | Gives a user permission to perform specific operations |
| `REVOKE` | Removes a previously granted permission |
| `SHOW GRANTS` | Lists what privileges a user currently has |
| `FLUSH PRIVILEGES` | Applies privilege changes immediately |
| Golden rule | Follow the **principle of least privilege** — only grant what a user actually needs |

---
📌 **Related notes:** [← TCL](f%5D-TCL.md) | [← DML](e%5D-DML.md) | [Back to README](README.md)
