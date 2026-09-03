# 🚀 MySQL Interview Practice — DDL, DML, TCL & DCL

> 🎯 **Goal:** Master the most important MySQL interview questions.
>
> 📚 **Topics:** DDL + DML + TCL + DCL
>
> ❌ **DQL is intentionally NOT included.**
>
> 🧠 Each topic contains:
> - 🟢 Easy — 10 questions
> - 🟡 Medium — 10 questions
> - 🔴 Hard — 10 questions
>
> 💪 **Total: 120 important questions with solutions**

---

# 📌 DATABASE USED FOR PRACTICE

~~~sql
CREATE DATABASE company_db;
USE company_db;

CREATE TABLE dept (
    deptno INT PRIMARY KEY,
    dname VARCHAR(50) NOT NULL,
    loc VARCHAR(50)
);

CREATE TABLE emp (
    empno INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    age INT,
    job VARCHAR(50),
    mgr INT,
    sal DECIMAL(10,2),
    comm DECIMAL(10,2),
    hiredate DATE,
    deptno INT,
    FOREIGN KEY (deptno) REFERENCES dept(deptno)
);
~~~

---

# 🟦 PART 1 — DDL

## 🟢 DDL — EASY — 10 QUESTIONS

### Q1. What is DDL?

**Answer:**

DDL stands for **Data Definition Language**.

It is used to define and modify the structure of database objects such as tables.

Common DDL commands:

- CREATE
- ALTER
- DROP
- TRUNCATE
- RENAME

---

### Q2. Create a table named `student` with `id` and `name`.

**Solution:**

~~~sql
CREATE TABLE student (
    id INT,
    name VARCHAR(50)
);
~~~

---

### Q3. Create a table with a primary key.

**Solution:**

~~~sql
CREATE TABLE employee (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(100)
);
~~~

---

### Q4. Add a new column `email` to the employee table.

**Solution:**

~~~sql
ALTER TABLE employee
ADD email VARCHAR(100);
~~~

---

### Q5. Change the datatype of `emp_name`.

**Solution:**

~~~sql
ALTER TABLE employee
MODIFY emp_name VARCHAR(150);
~~~

---

### Q6. Rename the employee table to staff.

**Solution:**

~~~sql
RENAME TABLE employee TO staff;
~~~

---

### Q7. Delete the entire employee table.

**Solution:**

~~~sql
DROP TABLE employee;
~~~

---

### Q8. Remove all rows from a table using TRUNCATE.

**Solution:**

~~~sql
TRUNCATE TABLE employee;
~~~

---

### Q9. Create a table only if it does not already exist.

**Solution:**

~~~sql
CREATE TABLE IF NOT EXISTS employee (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(100)
);
~~~

---

### Q10. Add a NOT NULL constraint to a column while creating a table.

**Solution:**

~~~sql
CREATE TABLE customer (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(100) NOT NULL
);
~~~

---

# 🟡 DDL — MEDIUM — 10 QUESTIONS

### Q11. Add a UNIQUE constraint to email.

**Solution:**

~~~sql
ALTER TABLE customer
ADD CONSTRAINT unique_email UNIQUE(email);
~~~

---

### Q12. Add a CHECK constraint that age must be at least 18.

**Solution:**

~~~sql
ALTER TABLE customer
ADD CONSTRAINT check_age CHECK(age >= 18);
~~~

---

### Q13. Add a DEFAULT value to city.

**Solution:**

~~~sql
ALTER TABLE customer
ALTER city SET DEFAULT 'Bangalore';
~~~

---

### Q14. Add a foreign key from emp.deptno to dept.deptno.

**Solution:**

~~~sql
ALTER TABLE emp
ADD CONSTRAINT fk_emp_dept
FOREIGN KEY (deptno)
REFERENCES dept(deptno);
~~~

---

### Q15. Remove a column called phone.

**Solution:**

~~~sql
ALTER TABLE customer
DROP COLUMN phone;
~~~

---

### Q16. Rename a column `name` to `full_name`.

**Solution:**

~~~sql
ALTER TABLE customer
RENAME COLUMN name TO full_name;
~~~

---

### Q17. Create a table containing an AUTO_INCREMENT primary key.

**Solution:**

~~~sql
CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    order_date DATE
);
~~~

---

### Q18. Remove the foreign key constraint from emp.

**Solution:**

~~~sql
ALTER TABLE emp
DROP FOREIGN KEY fk_emp_dept;
~~~

---

### Q19. Remove the UNIQUE constraint from email.

**Solution:**

~~~sql
ALTER TABLE customer
DROP INDEX unique_email;
~~~

---

### Q20. Create a table with multiple constraints.

**Solution:**

~~~sql
CREATE TABLE product (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) CHECK(price > 0),
    email VARCHAR(100) UNIQUE,
    category VARCHAR(50) DEFAULT 'General'
);
~~~

---

# 🔴 DDL — HARD — 10 QUESTIONS

### Q21. Explain the difference between DELETE, TRUNCATE and DROP.

**Answer:**

| Command | Removes Rows | Removes Structure | WHERE Allowed |
|---|---:|---:|---:|
| DELETE | Yes | No | Yes |
| TRUNCATE | Yes | No | No |
| DROP | Yes | Yes | No |

---

### Q22. Create a table with a composite primary key.

**Solution:**

~~~sql
CREATE TABLE enrollment (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    PRIMARY KEY (student_id, course_id)
);
~~~

---

### Q23. Add a composite UNIQUE constraint.

**Solution:**

~~~sql
ALTER TABLE employee
ADD CONSTRAINT unique_name
UNIQUE(first_name, last_name);
~~~

---

### Q24. Create a self-referencing foreign key for managers.

**Solution:**

~~~sql
ALTER TABLE emp
ADD CONSTRAINT fk_manager
FOREIGN KEY (mgr)
REFERENCES emp(empno);
~~~

---

### Q25. Create a table with ON DELETE CASCADE.

**Solution:**

~~~sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id)
        REFERENCES customer(customer_id)
        ON DELETE CASCADE
);
~~~

---

### Q26. Create a table with ON UPDATE CASCADE.

**Solution:**

~~~sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id)
        REFERENCES customer(customer_id)
        ON UPDATE CASCADE
);
~~~

---

### Q27. Create an index on the employee salary column.

**Solution:**

~~~sql
CREATE INDEX idx_salary
ON emp(sal);
~~~

---

### Q28. Drop the salary index.

**Solution:**

~~~sql
DROP INDEX idx_salary ON emp;
~~~

---

### Q29. Create a table with multiple foreign keys.

**Solution:**

~~~sql
CREATE TABLE project_assignment (
    empno INT,
    deptno INT,
    project_id INT,
    FOREIGN KEY (empno) REFERENCES emp(empno),
    FOREIGN KEY (deptno) REFERENCES dept(deptno)
);
~~~

---

### Q30. Why is TRUNCATE classified as DDL?

**Answer:**

`TRUNCATE` removes all rows by deallocating the table's data pages and resets the table's data storage state. It operates on the table structure rather than deleting rows one by one.

Therefore, MySQL classifies `TRUNCATE` as a **DDL operation**.

---

# 🟩 PART 2 — DML

## 🟢 DML — EASY — 10 QUESTIONS

### Q31. What is DML?

**Answer:**

DML stands for **Data Manipulation Language**.

It is used to manipulate data stored inside tables.

Important commands:

- INSERT
- UPDATE
- DELETE

---

### Q32. Insert one employee.

**Solution:**

~~~sql
INSERT INTO emp
(empno, first_name, last_name, age, job, sal, hiredate, deptno)
VALUES
(1033, 'Rahul', 'Kumar', 25, 'Data Analyst', 40000, '2026-08-01', 30);
~~~

---

### Q33. Insert multiple employees.

**Solution:**

~~~sql
INSERT INTO emp
(empno, first_name, last_name, age, job, sal, hiredate, deptno)
VALUES
(1034, 'Asha', 'R', 24, 'Data Analyst', 42000, '2026-08-02', 30),
(1035, 'Kiran', 'S', 26, 'Data Engineer', 50000, '2026-08-03', 40);
~~~

---

### Q34. Update an employee's salary.

**Solution:**

~~~sql
UPDATE emp
SET sal = 50000
WHERE empno = 1033;
~~~

---

### Q35. Increase salary by 10%.

**Solution:**

~~~sql
UPDATE emp
SET sal = sal * 1.10;
~~~

---

### Q36. Update the job of one employee.

**Solution:**

~~~sql
UPDATE emp
SET job = 'Senior Data Analyst'
WHERE empno = 1033;
~~~

---

### Q37. Delete one employee.

**Solution:**

~~~sql
DELETE FROM emp
WHERE empno = 1033;
~~~

---

### Q38. Delete all employees from a particular department.

**Solution:**

~~~sql
DELETE FROM emp
WHERE deptno = 30;
~~~

---

### Q39. Update the city/location of department 30.

**Solution:**

~~~sql
UPDATE dept
SET loc = 'Bangalore'
WHERE deptno = 30;
~~~

---

### Q40. Insert a department.

**Solution:**

~~~sql
INSERT INTO dept
(deptno, dname, loc)
VALUES
(60, 'Cloud Engineer', 'Bangalore');
~~~

---

# 🟡 DML — MEDIUM — 10 QUESTIONS

### Q41. Increase salary by 15% for Data Analysts.

**Solution:**

~~~sql
UPDATE emp
SET sal = sal * 1.15
WHERE job = 'Data Analyst';
~~~

---

### Q42. Increase salary by 10% for department 30.

**Solution:**

~~~sql
UPDATE emp
SET sal = sal * 1.10
WHERE deptno = 30;
~~~

---

### Q43. Give a 5000 salary increase to employees earning below 40000.

**Solution:**

~~~sql
UPDATE emp
SET sal = sal + 5000
WHERE sal < 40000;
~~~

---

### Q44. Delete employees whose salary is below 35000.

**Solution:**

~~~sql
DELETE FROM emp
WHERE sal < 35000;
~~~

---

### Q45. Delete employees whose department is 10 or 20.

**Solution:**

~~~sql
DELETE FROM emp
WHERE deptno IN (10, 20);
~~~

---

### Q46. Update commission to 1000 where commission is NULL.

**Solution:**

~~~sql
UPDATE emp
SET comm = 1000
WHERE comm IS NULL;
~~~

---

### Q47. Give a 20% salary increase to employees hired before 2021.

**Solution:**

~~~sql
UPDATE emp
SET sal = sal * 1.20
WHERE hiredate < '2021-01-01';
~~~

---

### Q48. Delete employees from the Data Analyst job.

**Solution:**

~~~sql
DELETE FROM emp
WHERE job = 'Data Analyst';
~~~

---

### Q49. Update employees in department 30 to department 40.

**Solution:**

~~~sql
UPDATE emp
SET deptno = 40
WHERE deptno = 30;
~~~

---

### Q50. Update the salary of the employee with the lowest salary by 5000.

**Solution:**

~~~sql
UPDATE emp
SET sal = sal + 5000
WHERE sal = (
    SELECT min_sal
    FROM (
        SELECT MIN(sal) AS min_sal
        FROM emp
    ) AS x
);
~~~

---

# 🔴 DML — HARD — 10 QUESTIONS

### Q51. Increase salary by 10% for employees whose salary is below the average salary.

**Solution:**

~~~sql
UPDATE emp
SET sal = sal * 1.10
WHERE sal < (
    SELECT avg_sal
    FROM (
        SELECT AVG(sal) AS avg_sal
        FROM emp
    ) AS x
);
~~~

---

### Q52. Give a 20% salary increase to employees earning less than the department average.

**Solution:**

~~~sql
UPDATE emp e
JOIN (
    SELECT deptno, AVG(sal) AS avg_sal
    FROM emp
    GROUP BY deptno
) d
ON e.deptno = d.deptno
SET e.sal = e.sal * 1.20
WHERE e.sal < d.avg_sal;
~~~

---

### Q53. Delete employees whose salary is below the average salary.

**Solution:**

~~~sql
DELETE FROM emp
WHERE sal < (
    SELECT avg_sal
    FROM (
        SELECT AVG(sal) AS avg_sal
        FROM emp
    ) AS x
);
~~~

---

### Q54. Delete employees who belong to departments located in Pune.

**Solution:**

~~~sql
DELETE e
FROM emp e
JOIN dept d
ON e.deptno = d.deptno
WHERE d.loc = 'Pune';
~~~

---

### Q55. Update employees in the department having the highest average salary.

**Solution:**

~~~sql
UPDATE emp e
JOIN (
    SELECT deptno
    FROM emp
    GROUP BY deptno
    ORDER BY AVG(sal) DESC
    LIMIT 1
) d
ON e.deptno = d.deptno
SET e.sal = e.sal * 1.10;
~~~

---

### Q56. Increase salary by 10% for the highest-paid employee in each department.

**Solution:**

~~~sql
UPDATE emp e
JOIN (
    SELECT deptno, MAX(sal) AS max_sal
    FROM emp
    GROUP BY deptno
) x
ON e.deptno = x.deptno
AND e.sal = x.max_sal
SET e.sal = e.sal * 1.10;
~~~

---

### Q57. Delete employees whose department has fewer than 3 employees.

**Solution:**

~~~sql
DELETE e
FROM emp e
JOIN (
    SELECT deptno
    FROM emp
    GROUP BY deptno
    HAVING COUNT(*) < 3
) x
ON e.deptno = x.deptno;
~~~

---

### Q58. Update all employees under manager 1001 with a 5% increment.

**Solution:**

~~~sql
UPDATE emp
SET sal = sal * 1.05
WHERE mgr = 1001;
~~~

---

### Q59. Move all Data Analysts to department 50.

**Solution:**

~~~sql
UPDATE emp
SET deptno = 50
WHERE job = 'Data Analyst';
~~~

---

### Q60. Delete employees hired before 2020 whose salary is below 50000.

**Solution:**

~~~sql
DELETE FROM emp
WHERE hiredate < '2020-01-01'
AND sal < 50000;
~~~

---

# 🟨 PART 3 — TCL

## 🟢 TCL — EASY — 10 QUESTIONS

### Q61. What is TCL?

**Answer:**

TCL stands for **Transaction Control Language**.

It controls transactions in a database.

Important TCL commands:

- COMMIT
- ROLLBACK
- SAVEPOINT

---

### Q62. What is a transaction?

**Answer:**

A transaction is a logical unit of work consisting of one or more SQL statements.

Example:

~~~sql
START TRANSACTION;

UPDATE emp
SET sal = sal + 5000
WHERE empno = 1001;

COMMIT;
~~~

---

### Q63. Start a transaction.

**Solution:**

~~~sql
START TRANSACTION;
~~~

---

### Q64. Commit a transaction.

**Solution:**

~~~sql
COMMIT;
~~~

---

### Q65. Roll back a transaction.

**Solution:**

~~~sql
ROLLBACK;
~~~

---

### Q66. Create a savepoint.

**Solution:**

~~~sql
SAVEPOINT sp1;
~~~

---

### Q67. Roll back to a savepoint.

**Solution:**

~~~sql
ROLLBACK TO SAVEPOINT sp1;
~~~

---

### Q68. Update salary and commit the change.

**Solution:**

~~~sql
START TRANSACTION;

UPDATE emp
SET sal = sal + 5000
WHERE empno = 1001;

COMMIT;
~~~

---

### Q69. Update salary and undo the change.

**Solution:**

~~~sql
START TRANSACTION;

UPDATE emp
SET sal = sal + 5000
WHERE empno = 1001;

ROLLBACK;
~~~

---

### Q70. Create two savepoints in one transaction.

**Solution:**

~~~sql
START TRANSACTION;

UPDATE emp
SET sal = sal + 1000
WHERE empno = 1001;

SAVEPOINT sp1;

UPDATE emp
SET sal = sal + 2000
WHERE empno = 1002;

SAVEPOINT sp2;
~~~

---

# 🟡 TCL — MEDIUM — 10 QUESTIONS

### Q71. Difference between COMMIT and ROLLBACK.

**Answer:**

`COMMIT` permanently saves the changes made in the transaction.

`ROLLBACK` reverses uncommitted changes.

---

### Q72. Difference between ROLLBACK and ROLLBACK TO SAVEPOINT.

**Answer:**

`ROLLBACK` reverses the entire current transaction.

`ROLLBACK TO SAVEPOINT` reverses changes made after the specified savepoint while keeping the transaction active.

---

### Q73. Transfer salary between two employees and commit.

**Solution:**

~~~sql
START TRANSACTION;

UPDATE emp
SET sal = sal - 5000
WHERE empno = 1001;

UPDATE emp
SET sal = sal + 5000
WHERE empno = 1002;

COMMIT;
~~~

---

### Q74. Transfer salary and roll back the transaction.

**Solution:**

~~~sql
START TRANSACTION;

UPDATE emp
SET sal = sal - 5000
WHERE empno = 1001;

UPDATE emp
SET sal = sal + 5000
WHERE empno = 1002;

ROLLBACK;
~~~

---

### Q75. Use a savepoint after the first update.

**Solution:**

~~~sql
START TRANSACTION;

UPDATE emp
SET sal = sal + 1000
WHERE empno = 1001;

SAVEPOINT first_update;

UPDATE emp
SET sal = sal + 2000
WHERE empno = 1002;

ROLLBACK TO SAVEPOINT first_update;

COMMIT;
~~~

---

### Q76. Explain why savepoints are useful.

**Answer:**

Savepoints allow you to partially undo a transaction instead of rolling back the entire transaction.

They are useful when a transaction contains multiple operations and only one section needs to be reversed.

---

### Q77. Create a transaction with three updates and undo only the third.

**Solution:**

~~~sql
START TRANSACTION;

UPDATE emp
SET sal = sal + 1000
WHERE empno = 1001;

UPDATE emp
SET sal = sal + 2000
WHERE empno = 1002;

SAVEPOINT before_third;

UPDATE emp
SET sal = sal + 3000
WHERE empno = 1003;

ROLLBACK TO SAVEPOINT before_third;

COMMIT;
~~~

---

### Q78. What happens after COMMIT?

**Answer:**

The changes made by the transaction are permanently saved and the transaction ends.

---

### Q79. What happens after ROLLBACK?

**Answer:**

Uncommitted changes in the current transaction are undone.

---

### Q80. Can you rollback after COMMIT?

**Answer:**

Normally, no.

Once the transaction has been committed, its changes have already been saved.

---

# 🔴 TCL — HARD — 10 QUESTIONS

### Q81. Perform a transaction with two savepoints and rollback to the first savepoint.

**Solution:**

~~~sql
START TRANSACTION;

UPDATE emp
SET sal = sal + 1000
WHERE empno = 1001;

SAVEPOINT sp1;

UPDATE emp
SET sal = sal + 2000
WHERE empno = 1002;

SAVEPOINT sp2;

UPDATE emp
SET sal = sal + 3000
WHERE empno = 1003;

ROLLBACK TO SAVEPOINT sp1;

COMMIT;
~~~

---

### Q82. Explain transaction atomicity.

**Answer:**

Atomicity means a transaction is treated as one unit.

Either all required operations succeed, or the transaction can be rolled back so that the partial work is not retained.

---

### Q83. What is the purpose of SAVEPOINT?

**Answer:**

A savepoint marks a position inside a transaction to which you can later roll back without cancelling the entire transaction.

---

### Q84. Create a transaction that updates employees in two departments and rollback if needed.

**Solution:**

~~~sql
START TRANSACTION;

UPDATE emp
SET sal = sal * 1.10
WHERE deptno = 30;

SAVEPOINT dept30_update;

UPDATE emp
SET sal = sal * 1.10
WHERE deptno = 40;

ROLLBACK TO SAVEPOINT dept30_update;

COMMIT;
~~~

---

### Q85. Explain why transactions are important in financial systems.

**Answer:**

Transactions ensure that related operations remain consistent.

For example, when transferring money:

1. Money must be deducted from one account.
2. Money must be added to another account.
3. Both operations should succeed together.

If something fails, the transaction can be rolled back.

---

### Q86. Write a transaction that updates salary, creates a savepoint, deletes an employee, then rolls back only the delete.

**Solution:**

~~~sql
START TRANSACTION;

UPDATE emp
SET sal = sal + 5000
WHERE empno = 1001;

SAVEPOINT salary_updated;

DELETE FROM emp
WHERE empno = 1033;

ROLLBACK TO SAVEPOINT salary_updated;

COMMIT;
~~~

---

### Q87. What is the difference between SAVEPOINT and COMMIT?

**Answer:**

`SAVEPOINT` creates a rollback point inside an active transaction.

`COMMIT` permanently saves the transaction and ends it.

---

### Q88. What happens to a savepoint after COMMIT?

**Answer:**

The savepoints belonging to that transaction are released because the transaction has ended.

---

### Q89. Can multiple savepoints exist in one transaction?

**Answer:**

Yes.

Example:

~~~sql
START TRANSACTION;

SAVEPOINT sp1;
SAVEPOINT sp2;
SAVEPOINT sp3;
~~~

---

### Q90. Write a complete transaction using INSERT, UPDATE, SAVEPOINT, ROLLBACK TO SAVEPOINT and COMMIT.

**Solution:**

~~~sql
START TRANSACTION;

INSERT INTO dept
(deptno, dname, loc)
VALUES
(60, 'Cloud Engineer', 'Bangalore');

SAVEPOINT dept_inserted;

UPDATE emp
SET sal = sal + 5000
WHERE deptno = 40;

ROLLBACK TO SAVEPOINT dept_inserted;

COMMIT;
~~~

---

# 🟥 PART 4 — DCL

## 🟢 DCL — EASY — 10 QUESTIONS

### Q91. What is DCL?

**Answer:**

DCL stands for **Data Control Language**.

It is used to control privileges and permissions on database objects.

Main commands:

- GRANT
- REVOKE

---

### Q92. What does GRANT do?

**Answer:**

`GRANT` gives privileges to a database user.

---

### Q93. What does REVOKE do?

**Answer:**

`REVOKE` removes previously granted privileges from a user.

---

### Q94. Grant SELECT permission on emp.

**Solution:**

~~~sql
GRANT SELECT
ON company_db.emp
TO 'analyst'@'localhost';
~~~

---

### Q95. Grant INSERT permission on emp.

**Solution:**

~~~sql
GRANT INSERT
ON company_db.emp
TO 'analyst'@'localhost';
~~~

---

### Q96. Grant UPDATE permission on emp.

**Solution:**

~~~sql
GRANT UPDATE
ON company_db.emp
TO 'analyst'@'localhost';
~~~

---

### Q97. Grant DELETE permission on emp.

**Solution:**

~~~sql
GRANT DELETE
ON company_db.emp
TO 'analyst'@'localhost';
~~~

---

### Q98. Revoke SELECT permission from a user.

**Solution:**

~~~sql
REVOKE SELECT
ON company_db.emp
FROM 'analyst'@'localhost';
~~~

---

### Q99. Grant multiple privileges.

**Solution:**

~~~sql
GRANT SELECT, INSERT, UPDATE
ON company_db.emp
TO 'analyst'@'localhost';
~~~

---

### Q100. Grant all privileges on a database.

**Solution:**

~~~sql
GRANT ALL PRIVILEGES
ON company_db.*
TO 'admin_user'@'localhost';
~~~

---

# 🟡 DCL — MEDIUM — 10 QUESTIONS

### Q101. Grant SELECT and INSERT on the department table.

**Solution:**

~~~sql
GRANT SELECT, INSERT
ON company_db.dept
TO 'analyst'@'localhost';
~~~

---

### Q102. Revoke INSERT permission from the user.

**Solution:**

~~~sql
REVOKE INSERT
ON company_db.dept
FROM 'analyst'@'localhost';
~~~

---

### Q103. Grant SELECT permission on all tables in company_db.

**Solution:**

~~~sql
GRANT SELECT
ON company_db.*
TO 'analyst'@'localhost';
~~~

---

### Q104. Revoke UPDATE permission on emp.

**Solution:**

~~~sql
REVOKE UPDATE
ON company_db.emp
FROM 'analyst'@'localhost';
~~~

---

### Q105. Grant SELECT and UPDATE on emp.

**Solution:**

~~~sql
GRANT SELECT, UPDATE
ON company_db.emp
TO 'analyst'@'localhost';
~~~

---

### Q106. Grant DELETE permission on a table.

**Solution:**

~~~sql
GRANT DELETE
ON company_db.emp
TO 'developer'@'localhost';
~~~

---

### Q107. Revoke DELETE permission.

**Solution:**

~~~sql
REVOKE DELETE
ON company_db.emp
FROM 'developer'@'localhost';
~~~

---

### Q108. What is the difference between database-level and table-level privileges?

**Answer:**

Database-level:

~~~sql
GRANT SELECT
ON company_db.*
TO 'analyst'@'localhost';
~~~

This applies to tables in the database.

Table-level:

~~~sql
GRANT SELECT
ON company_db.emp
TO 'analyst'@'localhost';
~~~

This applies only to the `emp` table.

---

### Q109. How can you see privileges granted to a user?

**Solution:**

~~~sql
SHOW GRANTS FOR 'analyst'@'localhost';
~~~

---

### Q110. Grant all privileges on one table.

**Solution:**

~~~sql
GRANT ALL PRIVILEGES
ON company_db.emp
TO 'admin_user'@'localhost';
~~~

---

# 🔴 DCL — HARD — 10 QUESTIONS

### Q111. Create a user and grant SELECT permission.

**Solution:**

~~~sql
CREATE USER 'analyst'@'localhost'
IDENTIFIED BY 'StrongPassword123!';

GRANT SELECT
ON company_db.*
TO 'analyst'@'localhost';
~~~

---

### Q112. Create a user and give SELECT and INSERT permissions.

**Solution:**

~~~sql
CREATE USER 'developer'@'localhost'
IDENTIFIED BY 'StrongPassword123!';

GRANT SELECT, INSERT
ON company_db.*
TO 'developer'@'localhost';
~~~

---

### Q113. Grant UPDATE only on salary-related employee data.

**Answer:**

For column-level privileges, specify the permitted columns.

**Solution:**

~~~sql
GRANT UPDATE(sal)
ON company_db.emp
TO 'salary_manager'@'localhost';
~~~

---

### Q114. Grant SELECT only on selected columns.

**Solution:**

~~~sql
GRANT SELECT(empno, first_name, last_name, job)
ON company_db.emp
TO 'report_user'@'localhost';
~~~

---

### Q115. Revoke all privileges from a user.

**Solution:**

~~~sql
REVOKE ALL PRIVILEGES, GRANT OPTION
FROM 'analyst'@'localhost';
~~~

---

### Q116. Explain GRANT OPTION.

**Answer:**

`WITH GRANT OPTION` allows a user to grant the privilege they received to other users.

Example:

~~~sql
GRANT SELECT
ON company_db.emp
TO 'manager'@'localhost'
WITH GRANT OPTION;
~~~

---

### Q117. Grant SELECT with GRANT OPTION.

**Solution:**

~~~sql
GRANT SELECT
ON company_db.emp
TO 'manager'@'localhost'
WITH GRANT OPTION;
~~~

---

### Q118. Explain the difference between GRANT and REVOKE.

**Answer:**

| Command | Purpose |
|---|---|
| GRANT | Gives privileges |
| REVOKE | Removes privileges |

Example:

~~~sql
GRANT SELECT
ON company_db.emp
TO 'analyst'@'localhost';

REVOKE SELECT
ON company_db.emp
FROM 'analyst'@'localhost';
~~~

---

### Q119. Give a user SELECT access to the whole database but UPDATE access only to emp.

**Solution:**

~~~sql
GRANT SELECT
ON company_db.*
TO 'analyst'@'localhost';

GRANT UPDATE
ON company_db.emp
TO 'analyst'@'localhost';
~~~

---

### Q120. Design privileges for three different users.

**Requirement:**

- Analyst → SELECT only
- Developer → SELECT, INSERT, UPDATE
- Admin → ALL PRIVILEGES

**Solution:**

~~~sql
CREATE USER 'analyst'@'localhost'
IDENTIFIED BY 'AnalystPassword123!';

CREATE USER 'developer'@'localhost'
IDENTIFIED BY 'DeveloperPassword123!';

CREATE USER 'admin_user'@'localhost'
IDENTIFIED BY 'AdminPassword123!';

GRANT SELECT
ON company_db.*
TO 'analyst'@'localhost';

GRANT SELECT, INSERT, UPDATE
ON company_db.*
TO 'developer'@'localhost';

GRANT ALL PRIVILEGES
ON company_db.*
TO 'admin_user'@'localhost';
~~~

---

# 🏆 FINAL INTERVIEW REVISION

## 🔥 DDL — Remember These

~~~text
CREATE     → Create database objects
ALTER      → Modify structure
DROP       → Remove structure
TRUNCATE   → Remove all rows
RENAME     → Rename object
~~~

## 🔥 DML — Remember These

~~~text
INSERT     → Add data
UPDATE     → Modify data
DELETE     → Remove data
~~~

## 🔥 TCL — Remember These

~~~text
START TRANSACTION → Begin transaction
COMMIT            → Save changes permanently
ROLLBACK          → Undo transaction
SAVEPOINT         → Create rollback point
ROLLBACK TO       → Undo to savepoint
~~~

## 🔥 DCL — Remember These

~~~text
GRANT       → Give permission
REVOKE      → Remove permission
~~~

---

# 🧠 MOST IMPORTANT INTERVIEW DIFFERENCES

## DELETE vs TRUNCATE vs DROP

~~~text
DELETE
→ Removes selected rows
→ WHERE can be used
→ Table structure remains

TRUNCATE
→ Removes all rows
→ WHERE cannot be used
→ Table structure remains
→ Classified as DDL in MySQL

DROP
→ Removes the entire table
→ Data + structure are removed
~~~

---

## DDL vs DML vs TCL vs DCL

| Category | Full Form | Main Purpose | Commands |
|---|---|---|---|
| DDL | Data Definition Language | Structure | CREATE, ALTER, DROP, TRUNCATE, RENAME |
| DML | Data Manipulation Language | Data | INSERT, UPDATE, DELETE |
| TCL | Transaction Control Language | Transactions | COMMIT, ROLLBACK, SAVEPOINT |
| DCL | Data Control Language | Permissions | GRANT, REVOKE |

---

# 🚀 120-QUESTION CHECKLIST

## DDL

- [x] 🟢 10 Easy
- [x] 🟡 10 Medium
- [x] 🔴 10 Hard

## DML

- [x] 🟢 10 Easy
- [x] 🟡 10 Medium
- [x] 🔴 10 Hard

## TCL

- [x] 🟢 10 Easy
- [x] 🟡 10 Medium
- [x] 🔴 10 Hard

## DCL

- [x] 🟢 10 Easy
- [x] 🟡 10 Medium
- [x] 🔴 10 Hard

# 🎯 TOTAL = 120 IMPORTANT INTERVIEW QUESTIONS

> 💪 **Don't just read the solutions.**
>
> First hide the solution → solve the question yourself → compare your answer → understand why the solution works.
>
> 🚀 **Master these 120 and your DDL, DML, TCL and DCL fundamentals will be interview-ready!**
