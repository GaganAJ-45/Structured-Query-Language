# Practice Set 3 — Subqueries, Set Operations & Single Row Functions

> ☕ Easy · ☕☕ Medium · ☕☕☕ Hard — grab a coffee, this one spans three files worth of concepts: `6.Subqueries.md`, `7.Set Operations.md`, and `9.Single Row Functions.md`.
>
> Rules stay the same as always: **questions first, solutions after the checkpoint.** No sneaking a peek. Try every question yourself — even a wrong, honest attempt teaches you more than reading someone else's correct answer first.

All questions run against the shared [`department`, `employee`, `project`](0.dataset.md) tables.

---

## ☕ Easy (5 questions)

**E1.** Write a query that returns every employee whose `salary` is above the company-wide average salary. (Hint: scalar subquery in `WHERE`.)

**E2.** Using a single row function, return every employee's `first_name` and `last_name` combined into one `full_name` column, in uppercase.

**E3.** Return a list of `dept_name` values from `department` for departments that currently have **at least one** employee. (Hint: `EXISTS` or `IN`.)

**E4.** Write a query that returns `first_name`, `hire_date`, and how many days each employee has been employed as of today, labeled `days_employed`.

**E5.** Combine the `dept_name` of every department with the `project_name` of every project into a single column called `item_name`, with no duplicate rows, using a set operation. (Yes, mixing departments and projects in one list is a little silly — that's fine, it's just for practicing the mechanics.)

---

## ☕☕ Medium (5 questions)

**M1.** Find every department whose `budget` is greater than the **average budget of all departments**. Show `dept_name` and `budget`.

**M2.** Write a query that returns each employee's `first_name`, `salary`, and their department's `dept_name` — but get the department name using a **subquery in the SELECT list**, not a `JOIN`.

**M3.** List every employee who is **not** currently assigned to any project. (Hint: `NOT IN` or `NOT EXISTS` against the `project` table — and remember the `NOT IN` + `NULL` trap from `6.Subqueries.md`.)

**M4.** Using `CONCAT()` and `DATE_FORMAT()`, produce one column called `profile_line` that reads like: `"Anjali Shah joined on 25 February 2022"` for every employee.

**M5.** Write one query that returns a single combined list of every **department manager's name** (`manager_name` from `department`) and every **project's lead employee's full name** (`first_name` + `' '` + `last_name` from `employee`, joined through `project`), labeled `person_name`, with duplicates kept (not removed).

---

## ☕☕☕ Hard (5 questions)

**H1.** Find the department(s) with the **highest average employee salary**, without using `LIMIT`. (Hint: compare against `MAX()` of a derived table of per-department averages — a subquery in `FROM`.)

**H2.** For every department, show `dept_name` and a column `status` that reads `'Has Projects'` if any of its employees have at least one project, or `'No Projects'` otherwise. (Hint: `CASE` + `EXISTS` subquery — or, if `CASE` hasn't been covered yet in your notes, just use two separate queries combined with `UNION` and a literal string column in each, same trick as `7.Set Operations.md`'s role-labeling example.)

**H3.** Using `ROUND()`, `COALESCE()`, and a correlated subquery, return every department's `dept_name` alongside its average employee salary rounded to the nearest thousand, showing `0` instead of `NULL` for departments with no employees or all-NULL salaries.

**H4.** Write a query using `UNION` that returns a combined list of: (a) employees earning **more** than their department's average salary, labeled `'Above Average'`, and (b) employees earning **less than or equal to** their department's average, labeled `'At or Below Average'` — one combined result set with columns `first_name`, `dept_id`, and `salary_status`.

**H5.** Find every employee whose `email` **local part** (the part before `@`) does not start with the lowercase version of their `first_name`. (Hint: combine `SUBSTRING()`, `LOWER()`, and comparison — this is a data-quality check query, the kind you'd actually run on a real production table.)

---

## ☕ Coffee Refill Checkpoint ☕

*Scroll past here only once you've attempted all 15. No judging, but also — no cheating.*

```




```

---

## Solutions

### Easy

**E1.**
```sql
SELECT first_name, last_name, salary
FROM employee
WHERE salary > (SELECT AVG(salary) FROM employee);
```

**E2.**
```sql
SELECT UPPER(CONCAT(first_name, ' ', last_name)) AS full_name
FROM employee;
```

**E3.**
```sql
SELECT dept_name
FROM department d
WHERE EXISTS (SELECT 1 FROM employee e WHERE e.dept_id = d.dept_id);
```

**E4.**
```sql
SELECT first_name, hire_date, DATEDIFF(CURDATE(), hire_date) AS days_employed
FROM employee;
```

**E5.**
```sql
SELECT dept_name AS item_name FROM department
UNION
SELECT project_name FROM project;
```

---

### Medium

**M1.**
```sql
SELECT dept_name, budget
FROM department
WHERE budget > (SELECT AVG(budget) FROM department);
```

**M2.**
```sql
SELECT 
    first_name, 
    salary,
    (SELECT d.dept_name FROM department d WHERE d.dept_id = e.dept_id) AS dept_name
FROM employee e;
```
> This is a **correlated subquery** — it reruns once per outer row, referencing `e.dept_id` from the outside. Works fine here, but for large tables a `JOIN` usually performs better. Good to know both ways exist.

**M3.**
```sql
SELECT first_name, last_name
FROM employee
WHERE emp_id NOT IN (
    SELECT emp_id FROM project WHERE emp_id IS NOT NULL
);
```
> The `WHERE emp_id IS NOT NULL` inside the subquery isn't optional here — without it, if even one `project.emp_id` were `NULL`, the entire `NOT IN` would silently return zero rows for everyone. (In this dataset `project.emp_id` never happens to be `NULL`, but writing it defensively is the right habit — see the NULL trap callout in `6.Subqueries.md`.)

**M4.**
```sql
SELECT CONCAT(first_name, ' ', last_name, ' joined on ', DATE_FORMAT(hire_date, '%d %M %Y')) AS profile_line
FROM employee;
```

**M5.**
```sql
SELECT manager_name AS person_name FROM department
UNION ALL
SELECT CONCAT(e.first_name, ' ', e.last_name) AS person_name
FROM project p
JOIN employee e ON p.emp_id = e.emp_id;
```
> `UNION ALL`, not `UNION` — the question says "duplicates kept," and since some employees lead more than one project, their name legitimately should appear more than once.

---

### Hard

**H1.**
```sql
SELECT dept_name, avg_salary
FROM (
    SELECT d.dept_name, AVG(e.salary) AS avg_salary
    FROM employee e
    JOIN department d ON e.dept_id = d.dept_id
    GROUP BY d.dept_name
) AS dept_averages
WHERE avg_salary = (
    SELECT MAX(avg_salary)
    FROM (
        SELECT AVG(e2.salary) AS avg_salary
        FROM employee e2
        GROUP BY e2.dept_id
    ) AS all_averages
);
```
> Two nested derived tables: the inner one computes every department's average salary, the outer `WHERE` keeps only the department(s) that hit the max of those averages. Using `=` instead of `LIMIT` means **ties** are kept — if two departments are tied for highest, you get both, which `ORDER BY ... LIMIT 1` would've silently hidden.

**H2.**
```sql
SELECT 
    dept_name,
    CASE 
        WHEN EXISTS (
            SELECT 1 FROM employee e 
            JOIN project p ON p.emp_id = e.emp_id 
            WHERE e.dept_id = d.dept_id
        ) THEN 'Has Projects'
        ELSE 'No Projects'
    END AS status
FROM department d;
```

**H3.**
```sql
SELECT 
    d.dept_name,
    COALESCE(ROUND((
        SELECT AVG(e.salary) 
        FROM employee e 
        WHERE e.dept_id = d.dept_id
    ), -3), 0) AS avg_salary_rounded
FROM department d;
```
> Reading inside-out: the correlated subquery finds the department's average salary (which is `NULL` if there are no employees, or if every salary in that department happens to be `NULL`) → `ROUND(..., -3)` rounds it to the nearest thousand (and `ROUND(NULL, -3)` is still `NULL`, rounding doesn't fix that) → `COALESCE(..., 0)` is the final safety net that turns any leftover `NULL` into a clean `0`.

**H4.**
```sql
SELECT e.first_name, e.dept_id, e.salary, 'Above Average' AS salary_status
FROM employee e
WHERE e.salary > (
    SELECT AVG(e2.salary) FROM employee e2 WHERE e2.dept_id = e.dept_id
)
UNION
SELECT e.first_name, e.dept_id, e.salary, 'At or Below Average' AS salary_status
FROM employee e
WHERE e.salary <= (
    SELECT AVG(e2.salary) FROM employee e2 WHERE e2.dept_id = e.dept_id
);
```
> Notice employees with a `NULL` salary vanish from **both halves** here — `NULL > anything` and `NULL <= anything` both evaluate to unknown, not true, so those rows just never qualify for either branch. If you wanted them included as a third labeled category, you'd need to add `OR e.salary IS NULL` logic explicitly — SQL never includes `NULL` rows by accident, only on purpose.

**H5.**
```sql
SELECT first_name, email
FROM employee
WHERE LOWER(SUBSTRING(email, 1, LENGTH(first_name))) != LOWER(first_name);
```
> `SUBSTRING(email, 1, LENGTH(first_name))` grabs exactly as many characters from the start of the email as `first_name` has — then compares that slice (lowercased) against the lowercased first name. Against this dataset's email pattern (`firstname.lastnameN@company.com`) this should return **zero rows** — which is itself a useful confirmation that the data is clean. Real production data rarely passes a check like this on the first try.

---

## Self-Check Scorecard

| Section | Questions | Your score |
|---|---|---|
| ☕ Easy | 5 | ___ / 5 |
| ☕☕ Medium | 5 | ___ / 5 |
| ☕☕☕ Hard | 5 | ___ / 5 |
| **Total** | **15** | **___ / 15** |

- **12–15:** Subqueries, set operations, and single row functions are solid — you're ready for anything the DQL folder has thrown at you so far.
- **7–11:** Good grip on the basics; revisit whichever section felt shakiest (correlated subqueries and the `NULL` traps trip up almost everyone at first).
- **0–6:** Worth a re-read of `6.Subqueries.md`, `7.Set Operations.md`, and `9.Single Row Functions.md` before trying this set again — these three topics lean on each other more than they first appear to.

---

**Related notes:**
[6. Subqueries](6.Subqueries.md) · [7. Set Operations](7.Set%20Operations.md) · [9. Single Row Functions](9.Single%20Row%20Functions.md) · [0. Dataset](0.dataset.md) · [README](../README.md)
