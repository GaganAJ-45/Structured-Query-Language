# TCL — Transaction Control Language

> Notes compiled from handwritten study material
> Topics: Transactions, COMMIT, ROLLBACK, SAVEPOINT — with full syntax and examples

---

## 1. What is TCL?

**TCL (Transaction Control Language)** is the part of SQL used to **manage transactions** — grouping one or more DML statements (`INSERT`, `UPDATE`, `DELETE`) together so they either **all succeed** or **all fail**, keeping the database consistent.

| Command | Purpose |
|---|---|
| `COMMIT` | Permanently save all changes made in the current transaction |
| `ROLLBACK` | Undo changes made in the current transaction (back to the last commit/savepoint) |
| `SAVEPOINT` | Mark a point within a transaction that you can roll back to, without undoing everything |

> ⚠️ TCL only works on **DML** statements (`INSERT`/`UPDATE`/`DELETE`). **DDL** statements (`CREATE`, `ALTER`, `DROP`, `TRUNCATE`) are auto-committed and **cannot** be rolled back — see the DDL notes.

---

## 2. What is a Transaction?

A **transaction** is a group of one or more SQL statements executed as a **single unit of work**. Either every statement in it succeeds, or none of them take effect — this keeps the database from ending up in a half-updated, inconsistent state.

**Example use case:** transferring money between two bank accounts — you need to *subtract* from one account AND *add* to another. If only one of those two steps succeeds (e.g., the app crashes in between), the data becomes wrong. A transaction ensures both happen together, or neither does.

### Starting a transaction
```sql
START TRANSACTION;
-- or
BEGIN;
```

---

## 3. COMMIT

**Saves all changes** made during the current transaction **permanently** to the database. Once committed, the changes **cannot** be undone with `ROLLBACK`.

```sql
START TRANSACTION;

UPDATE account SET balance = balance - 500 WHERE id = 1;
UPDATE account SET balance = balance + 500 WHERE id = 2;

COMMIT;  -- both updates are now permanent
```

> By default, MySQL runs in **autocommit mode** — every statement is committed immediately after it runs. `START TRANSACTION` temporarily turns autocommit off until you `COMMIT` or `ROLLBACK`.

---

## 4. ROLLBACK

**Undoes** all changes made since the last `COMMIT` (or since the transaction started, if nothing has been committed yet).

```sql
START TRANSACTION;

UPDATE account SET balance = balance - 500 WHERE id = 1;
UPDATE account SET balance = balance + 500 WHERE id = 2;

-- Something went wrong (e.g., account 2 doesn't exist)
ROLLBACK;  -- both updates are undone, balances go back to original
```

**Common use case:** wrapping risky operations in a transaction so that if any step fails (an error, a failed condition, a crash), you can safely revert everything with one command instead of manually fixing partial changes.

---

## 5. SAVEPOINT

Marks a **named checkpoint** inside a transaction. You can roll back **only to that point** instead of undoing the entire transaction — useful for multi-step transactions where you want partial rollback control.

```sql
START TRANSACTION;

INSERT INTO student (id, name) VALUES (1, 'AJ');
SAVEPOINT sp1;

INSERT INTO student (id, name) VALUES (2, 'Puneeth');
SAVEPOINT sp2;

INSERT INTO student (id, name) VALUES (3, 'Ravi');

-- Roll back only to sp2 — undoes the 3rd insert, keeps the first two
ROLLBACK TO sp2;

COMMIT;  -- only AJ and Puneeth get saved
```

### Releasing a savepoint (optional cleanup)
```sql
RELEASE SAVEPOINT sp1;  -- removes the savepoint, doesn't affect data
```

---

## 6. Full Worked Example

```sql
START TRANSACTION;

INSERT INTO orders (id, customer, amount) VALUES (101, 'AJ', 2500);
SAVEPOINT after_order;

UPDATE inventory SET stock = stock - 1 WHERE product_id = 55;

-- Suppose we realize the stock update was wrong
ROLLBACK TO after_order;   -- inventory update undone, order insert kept

COMMIT;   -- order insert is now permanently saved
```

---

## 7. COMMIT vs ROLLBACK vs SAVEPOINT — Comparison

| | `COMMIT` | `ROLLBACK` | `SAVEPOINT` |
|---|---|---|---|
| Action | Saves changes permanently | Undoes changes | Marks a checkpoint |
| Scope | Entire transaction | Entire transaction (or to a savepoint) | A specific point within a transaction |
| Reversible after use? | ❌ No | N/A | N/A (just a marker) |
| Typical use | End of a successful transaction | Error/failure recovery | Partial rollback control in multi-step transactions |

---

## 8. Quick Recap

| Command | What it does |
|---|---|
| `START TRANSACTION` | Begins a transaction, pausing autocommit |
| `COMMIT` | Permanently saves all changes in the transaction |
| `ROLLBACK` | Undoes changes back to the last commit (or a savepoint) |
| `SAVEPOINT` | Creates a named checkpoint to roll back to partially |
| Golden rule | TCL only controls **DML** changes — DDL is always auto-committed |

---
📌 **Related notes:** [← Data Manipulation Language](e%5D-Data%20Manipulation%20Language.md) | [Data Definition Language](d%5D-Data%20Definition%20Language.md) | [Back to README](README.md)
