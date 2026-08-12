# DQL Dataset — `department` & `employee` Tables

> This file is the **single source of truth** for the sample data used across every file in the DQL folder.
> Every query example in `1-basic-select-and-filtering.md`, and every file after it, runs against **these exact two tables** — so you never have to guess what data looks like, and results stay consistent from file to file.

---

## 1. Why these two tables?

A single flat table only teaches you `SELECT`/`WHERE`. Two related tables — a small `department` table and a bigger `employee` table linked by `dept_id` — let the same dataset carry you all the way from a basic `SELECT` up to `JOIN`s, `GROUP BY`, and subqueries, without switching context.

| Table | Rows | Columns | Purpose |
|---|---|---|---|
| `department` | 25 | 7 | The "small side" — good for simple filtering/sorting practice |
| `employee` | 100 | 10 | The "big side" — good for aggregates, grouping, joins, pagination |

> 😄 Yes, 25 departments for what looks like a mid-sized company is a *lot* — but more departments means more rows to `GROUP BY`, `JOIN`, and get creatively wrong with a missing `WHERE` clause. Consider it a company that really loves org charts.

---

## 2. `department` Table

### Schema
```sql
CREATE TABLE department (
    dept_id           INT PRIMARY KEY AUTO_INCREMENT,
    dept_name         VARCHAR(50) NOT NULL,
    location          VARCHAR(50),
    manager_name      VARCHAR(50),
    budget            NUMERIC(12, 2),
    established_year  INT,
    floor_no          INT
);
```

> ⚠️ **Why `INT AUTO_INCREMENT` instead of `SERIAL`?** In MySQL, `SERIAL` is just shorthand for `BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE`. If the parent table's key is `SERIAL` (→ `BIGINT UNSIGNED`) but the child table's foreign key column is plain `INT`, MySQL throws **Error 3780** ("referencing column and referenced column are incompatible") because the two types don't match byte-for-byte. Using plain `INT PRIMARY KEY AUTO_INCREMENT` on both sides keeps the types identical and the foreign key happy.

| Column | Type | Meaning |
|---|---|---|
| `dept_id` | `INT PRIMARY KEY AUTO_INCREMENT` | Unique ID for the department |
| `dept_name` | `VARCHAR(50)` | Name of the department (e.g. Engineering) |
| `location` | `VARCHAR(50)` | City the department is based in |
| `manager_name` | `VARCHAR(50)` | Name of the department head |
| `budget` | `NUMERIC(12,2)` | Annual budget in ₹ |
| `established_year` | `INT` | Year the department was set up |
| `floor_no` | `INT` | Office floor number |

### Preview (first 10 of 25 rows)
| dept_id | dept_name | location | manager_name | budget | established_year | floor_no |
|---|---|---|---|---|---|---|
| 1 | Engineering | Delhi | Rohan Sharma | 1800000 | 2021 | 5 |
| 2 | Sales | Mumbai | Ananya Reddy | 3200000 | 2021 | 2 |
| 3 | Human Resources | Delhi | Kiran Verma | 2600000 | 2016 | 7 |
| 4 | Marketing | Bengaluru | Deepa Rao | 2600000 | 2004 | 4 |
| 5 | Finance | Pune | Sanjay Pillai | 1800000 | 2015 | 4 |
| 6 | IT Support | Delhi | Priya Verma | 6800000 | 2005 | 8 |
| 7 | Legal | Pune | Manoj Nair | 11800000 | 1998 | 3 |
| 8 | Research & Development | Delhi | Divya Iyer | 5800000 | 2006 | 3 |
| 9 | Design | Mumbai | Arjun Gupta | 2800000 | 2000 | 7 |
| 10 | Operations | Bengaluru | Neha Gupta | 5900000 | 2017 | 5 |

> Full 25-row `INSERT` statement is in Section 4 below — copy-paste it into your own MySQL instance to actually run every example query yourself. Reading queries is fine; *running* them is how it sticks.

---

## 3. `employee` Table

### Schema
```sql
CREATE TABLE employee (
    emp_id       INT PRIMARY KEY AUTO_INCREMENT,
    first_name   VARCHAR(50) NOT NULL,
    last_name    VARCHAR(50),
    age          INT CHECK (age > 0),
    gender       ENUM('Male', 'Female'),
    salary       NUMERIC(10, 2),
    email        VARCHAR(100) UNIQUE,
    dept_id      INT,
    hire_date    DATE,
    designation  VARCHAR(50),
    FOREIGN KEY (dept_id) REFERENCES department(dept_id)
);
```

> `dept_id` here is plain `INT`, matching `department.dept_id` (also `INT`) exactly — that's what makes the `FOREIGN KEY` line work without error.

| Column | Type | Meaning |
|---|---|---|
| `emp_id` | `INT PRIMARY KEY AUTO_INCREMENT` | Unique ID for the employee |
| `first_name` | `VARCHAR(50)` | First name |
| `last_name` | `VARCHAR(50)` | Last name |
| `age` | `INT` | Age in years |
| `gender` | `ENUM('Male','Female')` | Gender |
| `salary` | `NUMERIC(10,2)` | Monthly salary in ₹ — **a few rows are intentionally `NULL`**, on purpose, for later `IS NULL` practice |
| `email` | `VARCHAR(100)` | Unique work email |
| `dept_id` | `INT` (FK) | Links to `department.dept_id` |
| `hire_date` | `DATE` | Date the employee joined |
| `designation` | `VARCHAR(50)` | Job title/role |

### Preview (first 10 of 100 rows)
| emp_id | first_name | last_name | age | gender | salary | email | dept_id | hire_date | designation |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Anjali | Shah | 46 | Female | 43131 | anjali.shah1@company.com | 17 | 2022-02-25 | Junior Developer |
| 2 | Kiara | Rao | 31 | Female | 75432 | kiara.rao2@company.com | 13 | 2024-08-17 | Intern |
| 3 | Preeti | Sharma | 28 | Female | 69587 | preeti.sharma3@company.com | 4 | 2019-07-06 | Consultant |
| 4 | Aarav | Hegde | 37 | Male | 38947 | aarav.hegde4@company.com | 21 | 2019-11-17 | Executive |
| 5 | Neha | Rao | 44 | Male | 127056 | neha.rao5@company.com | 17 | 2015-10-11 | Consultant |
| 6 | Aditya | Kumar | 44 | Female | 56571 | aditya.kumar6@company.com | 19 | 2016-02-24 | Consultant |
| 7 | Krishna | Shetty | 55 | Male | 87296 | krishna.shetty7@company.com | 18 | 2017-05-17 | Executive |
| 8 | Naina | Nair | 55 | Male | 77296 | naina.nair8@company.com | 22 | 2025-06-15 | Associate |
| 9 | Simran | Kumar | 36 | Male | NULL | simran.kumar9@company.com | 1 | 2024-09-08 | Executive |
| 10 | Sneha | Sharma | 25 | Male | 143675 | sneha.sharma10@company.com | 2 | 2020-02-17 | Manager |

> Notice `emp_id 4` (and a few others further down) has a `NULL` salary — that wasn't a data-entry mistake, it's deliberate. Real payroll tables have gaps like this (new joiners not yet processed), and it's the perfect excuse to introduce `IS NULL` later without inventing a fake edge case.

---
## 4. Full Setup Script (copy-paste to build both tables)

Run this once in your MySQL client and you have the exact same 25 departments and 100 employees used throughout every DQL note.

```sql
-- 1. Create the database (skip if you already have one)
CREATE DATABASE IF NOT EXISTS company;
USE company;

-- 2. Create the tables
-- Note: using INT AUTO_INCREMENT (not SERIAL) on both tables so the
-- primary key and foreign key column types match exactly — see the
-- warning in Section 2 for why this matters.
CREATE TABLE department (
    dept_id           INT PRIMARY KEY AUTO_INCREMENT,
    dept_name         VARCHAR(50) NOT NULL,
    location          VARCHAR(50),
    manager_name      VARCHAR(50),
    budget            NUMERIC(12, 2),
    established_year  INT,
    floor_no          INT
);

CREATE TABLE employee (
    emp_id       INT PRIMARY KEY AUTO_INCREMENT,
    first_name   VARCHAR(50) NOT NULL,
    last_name    VARCHAR(50),
    age          INT CHECK (age > 0),
    gender       ENUM('Male', 'Female'),
    salary       NUMERIC(10, 2),
    email        VARCHAR(100) UNIQUE,
    dept_id      INT,
    hire_date    DATE,
    designation  VARCHAR(50),
    FOREIGN KEY (dept_id) REFERENCES department(dept_id)
);

-- 3. Insert 25 departments
INSERT INTO department (dept_id, dept_name, location, manager_name, budget, established_year, floor_no) VALUES
    (1, 'Engineering', 'Delhi', 'Rohan Sharma', 1800000, 2021, 5),
    (2, 'Sales', 'Mumbai', 'Ananya Reddy', 3200000, 2021, 2),
    (3, 'Human Resources', 'Delhi', 'Kiran Verma', 2600000, 2016, 7),
    (4, 'Marketing', 'Bengaluru', 'Deepa Rao', 2600000, 2004, 4),
    (5, 'Finance', 'Pune', 'Sanjay Pillai', 1800000, 2015, 4),
    (6, 'IT Support', 'Delhi', 'Priya Verma', 6800000, 2005, 8),
    (7, 'Legal', 'Pune', 'Manoj Nair', 11800000, 1998, 3),
    (8, 'Research & Development', 'Delhi', 'Divya Iyer', 5800000, 2006, 3),
    (9, 'Design', 'Mumbai', 'Arjun Gupta', 2800000, 2000, 7),
    (10, 'Operations', 'Bengaluru', 'Neha Gupta', 5900000, 2017, 5),
    (11, 'Procurement', 'Noida', 'Vikram Rao', 10800000, 2012, 9),
    (12, 'Customer Support', 'Bengaluru', 'Pooja Iyer', 2500000, 2015, 5),
    (13, 'Data Science', 'Noida', 'Suresh Pillai', 6100000, 2016, 4),
    (14, 'Quality Assurance', 'Delhi', 'Ritu Sharma', 2000000, 2019, 4),
    (15, 'DevOps', 'Noida', 'Karthik Nair', 2500000, 2005, 2),
    (16, 'Security', 'Chennai', 'Meena Nair', 7300000, 2018, 6),
    (17, 'Facilities', 'Mumbai', 'Anil Gupta', 6000000, 2004, 11),
    (18, 'Logistics', 'Hyderabad', 'Sneha Sharma', 9200000, 2018, 3),
    (19, 'Public Relations', 'Pune', 'Rahul Reddy', 3500000, 2012, 7),
    (20, 'Business Development', 'Hyderabad', 'Nisha Verma', 4300000, 2019, 6),
    (21, 'Compliance', 'Noida', 'Ganesh Rao', 4400000, 1999, 6),
    (22, 'Training', 'Chennai', 'Kavya Nair', 2300000, 2004, 10),
    (23, 'Analytics', 'Delhi', 'Ajay Gupta', 4200000, 2018, 8),
    (24, 'Product Management', 'Chennai', 'Swati Menon', 3300000, 2006, 3),
    (25, 'Administration', 'Mumbai', 'Vishal Verma', 8300000, 2006, 12);

-- 4. Insert 100 employees
INSERT INTO employee (emp_id, first_name, last_name, age, gender, salary, email, dept_id, hire_date, designation) VALUES
    (1, 'Anjali', 'Shah', 46, 'Female', 43131, 'anjali.shah1@company.com', 17, '2022-02-25', 'Junior Developer'),
    (2, 'Kiara', 'Rao', 31, 'Female', 75432, 'kiara.rao2@company.com', 13, '2024-08-17', 'Intern'),
    (3, 'Preeti', 'Sharma', 28, 'Female', 69587, 'preeti.sharma3@company.com', 4, '2019-07-06', 'Consultant'),
    (4, 'Aarav', 'Hegde', 37, 'Male', 38947, 'aarav.hegde4@company.com', 21, '2019-11-17', 'Executive'),
    (5, 'Neha', 'Rao', 44, 'Male', 127056, 'neha.rao5@company.com', 17, '2015-10-11', 'Consultant'),
    (6, 'Aditya', 'Kumar', 44, 'Female', 56571, 'aditya.kumar6@company.com', 19, '2016-02-24', 'Consultant'),
    (7, 'Krishna', 'Shetty', 55, 'Male', 87296, 'krishna.shetty7@company.com', 18, '2017-05-17', 'Executive'),
    (8, 'Naina', 'Nair', 55, 'Male', 77296, 'naina.nair8@company.com', 22, '2025-06-15', 'Associate'),
    (9, 'Simran', 'Kumar', 36, 'Male', NULL, 'simran.kumar9@company.com', 1, '2024-09-08', 'Executive'),
    (10, 'Sneha', 'Sharma', 25, 'Male', 143675, 'sneha.sharma10@company.com', 2, '2020-02-17', 'Manager'),
    (11, 'Deepa', 'Deshmukh', 52, 'Male', 119811, 'deepa.deshmukh11@company.com', 19, '2024-08-08', 'Consultant'),
    (12, 'Aisha', 'Nair', 27, 'Male', 71438, 'aisha.nair12@company.com', 14, '2021-08-28', 'Junior Developer'),
    (13, 'Dinesh', 'Kulkarni', 27, 'Male', 69473, 'dinesh.kulkarni13@company.com', 4, '2018-04-07', 'Associate'),
    (14, 'Simran', 'Rao', 48, 'Male', 57742, 'simran.rao14@company.com', 3, '2022-09-04', 'Junior Developer'),
    (15, 'Rajesh', 'Bose', 21, 'Male', 136240, 'rajesh.bose15@company.com', 8, '2017-07-16', 'Consultant'),
    (16, 'Pooja', 'Mehta', 24, 'Male', 76173, 'pooja.mehta16@company.com', 9, '2022-05-14', 'Associate'),
    (17, 'Mahesh', 'Bhat', 52, 'Male', 53534, 'mahesh.bhat17@company.com', 2, '2024-12-18', 'Junior Developer'),
    (18, 'Amit', 'Joshi', 24, 'Male', 90909, 'amit.joshi18@company.com', 17, '2017-01-17', 'Senior Developer'),
    (19, 'Dev', 'Gupta', 25, 'Male', 148416, 'dev.gupta19@company.com', 19, '2018-10-20', 'Junior Developer'),
    (20, 'Geeta', 'Gupta', 47, 'Female', 51772, 'geeta.gupta20@company.com', 22, '2026-06-08', 'Intern'),
    (21, 'Ira', 'Rao', 40, 'Female', 123548, 'ira.rao21@company.com', 3, '2015-08-20', 'Executive'),
    (22, 'Saanvi', 'Gupta', 55, 'Male', 42361, 'saanvi.gupta22@company.com', 12, '2016-04-12', 'Intern'),
    (23, 'Rohan', 'Patel', 55, 'Female', 130788, 'rohan.patel23@company.com', 21, '2023-01-22', 'Associate'),
    (24, 'Advik', 'Deshmukh', 27, 'Male', 141616, 'advik.deshmukh24@company.com', 4, '2026-09-05', 'Intern'),
    (25, 'Arnav', 'Das', 34, 'Female', 108130, 'arnav.das25@company.com', 9, '2023-08-09', 'Junior Developer'),
    (26, 'Diya', 'Kulkarni', 48, 'Female', NULL, 'diya.kulkarni26@company.com', 11, '2017-11-09', 'Team Lead'),
    (27, 'Sumit', 'Patel', 48, 'Male', 148869, 'sumit.patel27@company.com', 23, '2017-09-02', 'Analyst'),
    (28, 'Anjali', 'Bose', 30, 'Female', 65404, 'anjali.bose28@company.com', 12, '2015-06-07', 'Manager'),
    (29, 'Naresh', 'Kumar', 43, 'Female', 123235, 'naresh.kumar29@company.com', 5, '2018-03-26', 'Team Lead'),
    (30, 'Aisha', 'Sharma', 32, 'Female', 78964, 'aisha.sharma30@company.com', 22, '2026-04-09', 'Team Lead'),
    (31, 'Yogesh', 'Kumar', 45, 'Male', 54154, 'yogesh.kumar31@company.com', 7, '2022-06-10', 'Manager'),
    (32, 'Sneha', 'Sharma', 33, 'Female', 138292, 'sneha.sharma32@company.com', 3, '2019-06-21', 'Associate'),
    (33, 'Zara', 'Deshmukh', 55, 'Female', 40118, 'zara.deshmukh33@company.com', 9, '2017-10-09', 'Junior Developer'),
    (34, 'Aadhya', 'Das', 48, 'Female', 66114, 'aadhya.das34@company.com', 14, '2024-09-04', 'Senior Analyst'),
    (35, 'Shreya', 'Nair', 37, 'Male', 25221, 'shreya.nair35@company.com', 17, '2023-11-24', 'Manager'),
    (36, 'Rahul', 'Shah', 25, 'Female', 111951, 'rahul.shah36@company.com', 4, '2026-05-17', 'Intern'),
    (37, 'Naresh', 'Shah', 41, 'Female', 97667, 'naresh.shah37@company.com', 5, '2018-07-22', 'Senior Analyst'),
    (38, 'Dinesh', 'Hegde', 32, 'Female', 134278, 'dinesh.hegde38@company.com', 1, '2019-05-07', 'Senior Analyst'),
    (39, 'Anjali', 'Das', 41, 'Female', 113555, 'anjali.das39@company.com', 7, '2023-08-26', 'Team Lead'),
    (40, 'Mahesh', 'Gupta', 39, 'Female', 149780, 'mahesh.gupta40@company.com', 25, '2018-11-10', 'Manager'),
    (41, 'Neha', 'Rao', 22, 'Male', 87277, 'neha.rao41@company.com', 20, '2016-08-14', 'Executive'),
    (42, 'Aryan', 'Bhat', 45, 'Female', 44342, 'aryan.bhat42@company.com', 21, '2026-01-25', 'Senior Developer'),
    (43, 'Piyush', 'Shah', 35, 'Male', 116214, 'piyush.shah43@company.com', 17, '2022-01-18', 'Manager'),
    (44, 'Myra', 'Patel', 29, 'Female', 98259, 'myra.patel44@company.com', 20, '2020-08-20', 'Associate'),
    (45, 'Naina', 'Bose', 49, 'Male', 87216, 'naina.bose45@company.com', 15, '2019-04-27', 'Intern'),
    (46, 'Saurabh', 'Shetty', 54, 'Female', 60992, 'saurabh.shetty46@company.com', 15, '2016-12-10', 'Manager'),
    (47, 'Nisha', 'Joshi', 41, 'Male', 55311, 'nisha.joshi47@company.com', 13, '2026-03-23', 'Manager'),
    (48, 'Krishna', 'Shah', 47, 'Female', 79496, 'krishna.shah48@company.com', 2, '2018-07-13', 'Executive'),
    (49, 'Yogesh', 'Sharma', 45, 'Female', NULL, 'yogesh.sharma49@company.com', 12, '2019-07-28', 'Senior Analyst'),
    (50, 'Nikhil', 'Hegde', 55, 'Male', 60774, 'nikhil.hegde50@company.com', 14, '2022-01-13', 'Analyst'),
    (51, 'Naresh', 'Deshmukh', 46, 'Male', 145526, 'naresh.deshmukh51@company.com', 5, '2024-09-01', 'Senior Analyst'),
    (52, 'Rekha', 'Mukherjee', 22, 'Male', 42786, 'rekha.mukherjee52@company.com', 15, '2017-01-09', 'Senior Analyst'),
    (53, 'Manoj', 'Nair', 50, 'Female', 140296, 'manoj.nair53@company.com', 13, '2019-07-09', 'Senior Developer'),
    (54, 'Dhruv', 'Sharma', 55, 'Male', 70870, 'dhruv.sharma54@company.com', 8, '2025-02-25', 'Junior Developer'),
    (55, 'Vipul', 'Sharma', 36, 'Male', 106439, 'vipul.sharma55@company.com', 5, '2018-03-16', 'Senior Developer'),
    (56, 'Bhavna', 'Nair', 50, 'Female', 46992, 'bhavna.nair56@company.com', 20, '2024-12-23', 'Senior Developer'),
    (57, 'Piyush', 'Reddy', 40, 'Male', 146751, 'piyush.reddy57@company.com', 10, '2024-11-13', 'Senior Analyst'),
    (58, 'Kunal', 'Nair', 25, 'Male', 126250, 'kunal.nair58@company.com', 10, '2025-10-26', 'Senior Developer'),
    (59, 'Bhavna', 'Verma', 43, 'Female', 34038, 'bhavna.verma59@company.com', 17, '2025-06-01', 'Senior Analyst'),
    (60, 'Farhan', 'Kumar', 48, 'Female', 133620, 'farhan.kumar60@company.com', 15, '2026-03-14', 'Team Lead'),
    (61, 'Mohit', 'Chowdhury', 38, 'Female', 133230, 'mohit.chowdhury61@company.com', 24, '2024-05-11', 'Manager'),
    (62, 'Diya', 'Menon', 49, 'Male', 99691, 'diya.menon62@company.com', 20, '2025-07-11', 'Junior Developer'),
    (63, 'Imran', 'Joshi', 32, 'Female', 129573, 'imran.joshi63@company.com', 9, '2020-05-20', 'Intern'),
    (64, 'Komal', 'Sharma', 54, 'Male', 119381, 'komal.sharma64@company.com', 14, '2022-09-25', 'Manager'),
    (65, 'Umesh', 'Naidu', 52, 'Female', 37196, 'umesh.naidu65@company.com', 10, '2018-07-23', 'Manager'),
    (66, 'Shaurya', 'Deshmukh', 44, 'Female', 70056, 'shaurya.deshmukh66@company.com', 14, '2026-09-11', 'Analyst'),
    (67, 'Yogesh', 'Patel', 38, 'Female', 40814, 'yogesh.patel67@company.com', 24, '2018-06-04', 'Associate'),
    (68, 'Gaurav', 'Bhat', 32, 'Male', 88464, 'gaurav.bhat68@company.com', 9, '2026-10-25', 'Associate'),
    (69, 'Sunita', 'Pillai', 27, 'Male', 72301, 'sunita.pillai69@company.com', 6, '2019-01-23', 'Associate'),
    (70, 'Pari', 'Menon', 23, 'Male', 116411, 'pari.menon70@company.com', 5, '2025-08-04', 'Junior Developer'),
    (71, 'Shreya', 'Pillai', 51, 'Female', 49164, 'shreya.pillai71@company.com', 2, '2019-08-04', 'Senior Developer'),
    (72, 'Zara', 'Naidu', 25, 'Male', 131318, 'zara.naidu72@company.com', 19, '2019-02-08', 'Senior Developer'),
    (73, 'Komal', 'Shetty', 47, 'Male', 74856, 'komal.shetty73@company.com', 15, '2022-05-28', 'Executive'),
    (74, 'Naina', 'Pillai', 24, 'Male', 52235, 'naina.pillai74@company.com', 21, '2018-05-22', 'Senior Developer'),
    (75, 'Rohan', 'Iyer', 32, 'Male', 78545, 'rohan.iyer75@company.com', 15, '2026-10-16', 'Intern'),
    (76, 'Arjun', 'Iyer', 39, 'Female', 84510, 'arjun.iyer76@company.com', 3, '2025-04-09', 'Executive'),
    (77, 'Mahesh', 'Nair', 48, 'Male', 109886, 'mahesh.nair77@company.com', 5, '2019-03-03', 'Junior Developer'),
    (78, 'Kabir', 'Pillai', 39, 'Female', 115266, 'kabir.pillai78@company.com', 10, '2026-07-09', 'Associate'),
    (79, 'Varun', 'Naidu', 49, 'Male', 141578, 'varun.naidu79@company.com', 14, '2026-06-20', 'Intern'),
    (80, 'Vihaan', 'Gupta', 35, 'Male', 113117, 'vihaan.gupta80@company.com', 9, '2024-01-25', 'Team Lead'),
    (81, 'Dhruv', 'Chowdhury', 49, 'Female', 101720, 'dhruv.chowdhury81@company.com', 14, '2025-08-03', 'Consultant'),
    (82, 'Vikram', 'Shah', 42, 'Female', 137424, 'vikram.shah82@company.com', 6, '2020-07-23', 'Consultant'),
    (83, 'Arnav', 'Deshmukh', 46, 'Male', 66224, 'arnav.deshmukh83@company.com', 9, '2020-02-25', 'Senior Analyst'),
    (84, 'Rehan', 'Sharma', 55, 'Female', 49586, 'rehan.sharma84@company.com', 17, '2020-10-25', 'Consultant'),
    (85, 'Harsh', 'Patel', 24, 'Male', 42172, 'harsh.patel85@company.com', 10, '2022-12-16', 'Senior Developer'),
    (86, 'Vihaan', 'Kulkarni', 36, 'Male', 26792, 'vihaan.kulkarni86@company.com', 18, '2021-02-08', 'Senior Developer'),
    (87, 'Alia', 'Kumar', 30, 'Female', 63254, 'alia.kumar87@company.com', 17, '2026-05-14', 'Consultant'),
    (88, 'Dhruv', 'Iyer', 50, 'Male', 145831, 'dhruv.iyer88@company.com', 20, '2023-12-05', 'Senior Developer'),
    (89, 'Deepa', 'Shetty', 47, 'Female', 91550, 'deepa.shetty89@company.com', 9, '2015-05-24', 'Intern'),
    (90, 'Rekha', 'Mukherjee', 52, 'Male', 88483, 'rekha.mukherjee90@company.com', 12, '2020-09-25', 'Associate'),
    (91, 'Vishal', 'Patel', 41, 'Male', 56300, 'vishal.patel91@company.com', 19, '2021-04-28', 'Senior Analyst'),
    (92, 'Sai', 'Joshi', 51, 'Female', 111991, 'sai.joshi92@company.com', 21, '2017-08-02', 'Team Lead'),
    (93, 'Zaid', 'Mukherjee', 42, 'Male', 82711, 'zaid.mukherjee93@company.com', 4, '2023-08-01', 'Team Lead'),
    (94, 'Aisha', 'Kulkarni', 30, 'Male', 59737, 'aisha.kulkarni94@company.com', 11, '2024-12-13', 'Senior Developer'),
    (95, 'Suresh', 'Deshmukh', 55, 'Female', 107155, 'suresh.deshmukh95@company.com', 23, '2022-09-02', 'Executive'),
    (96, 'Krishna', 'Iyer', 39, 'Male', 81881, 'krishna.iyer96@company.com', 4, '2025-12-28', 'Senior Developer'),
    (97, 'Radhika', 'Reddy', 40, 'Male', NULL, 'radhika.reddy97@company.com', 2, '2019-06-12', 'Senior Analyst'),
    (98, 'Navya', 'Iyer', 54, 'Female', 128877, 'navya.iyer98@company.com', 6, '2017-03-03', 'Executive'),
    (99, 'Vishal', 'Das', 36, 'Female', 43758, 'vishal.das99@company.com', 8, '2022-11-09', 'Consultant'),
    (100, 'Ritu', 'Deshmukh', 21, 'Female', 113815, 'ritu.deshmukh100@company.com', 18, '2017-02-15', 'Analyst');
```

---

## 5. Quick Sanity-Check Queries

Once you've run the setup script, these two one-liners confirm everything loaded correctly (feel free to run them before you even start the Basic SELECT file):

```sql
SELECT COUNT(*) FROM department;   -- should return 25
SELECT COUNT(*) FROM employee;     -- should return 100
```

---
📌 **Used by:** [1. Basic SELECT & Filtering →](1-basic-select-and-filtering.md) | [Back to README](../README.md)
