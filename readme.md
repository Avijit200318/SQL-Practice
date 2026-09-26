# 🗄️ Master SQL: From Fundamentals to LeetCode Solutions

Welcome to my complete SQL repository! This single document contains all my SQL cheat sheets, foundational notes, and structured solutions to popular LeetCode database problems.

---

## 🚀 Learning Roadmap & Quick Start

To get the most out of this repository, follow this recommended learning sequence:

1. **Read `start.doc` First**: Open and review the `start.doc` file in this repository. It covers core database concepts, relational schemas, basic syntax, and data types before you jump into practical problems.
2. **Follow the Topic Sections**: Work through the SQL sections below in order. They progress naturally from basic data retrieval to advanced analytical queries.
3. **Solve LeetCode SQL 50**: Then visit the LeetCode 50 Days SQL challenge and solve them; all the answers and concepts are written here.

---

## 📚 Table of Contents
* 1. Select
* 2. Basic Joins
* 3. Basic Aggregate Functions
* 4. Sorting and Grouping
* 5. Advanced Select and Joins
* 6. Subqueries
* 7. Advanced String Functions / Regex / Clause

---

## 1. Select
Covers basic column extraction, filtering rows using `WHERE`, conditional expressions using `CASE WHEN`, and handling `NULL` values.

---

## 2. Basic Joins
Covers combining data across multiple tables using `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, and self-joins.

> **Key Rule (`UNION` vs `UNION ALL`):**  
> Always use `UNION ALL` if you want to keep duplicate rows or guarantee that two identical results both appear in your output. Standard `UNION` automatically removes duplicate rows across the combined dataset.

---

## 3. Basic Aggregate Functions
Covers summarizing datasets using `COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`, and filtering aggregated results with `HAVING`.

---

## 4. Sorting and Grouping
Covers ordering results with `ORDER BY`, grouping data with `GROUP BY`, and eliminating duplicate rows using `DISTINCT`.

---

## 5. Advanced Select and Joins
Covers complex join logic, consecutive row matching, non-equi joins, and multi-table filtering.

---

## 6. Subqueries
Covers scalar subqueries, correlated subqueries, `IN` / `NOT IN` clauses, and using Common Table Expressions (`WITH ... AS`) to break down complex queries into readable blocks.

---

## 7. Advanced String Functions / Regex / Clause

### Window Functions & Ranking
Window functions allow you to perform calculations across a set of table rows related to the current row without collapsing the dataset into a single summary row.

* **`ROW_NUMBER()`**: Numbers every row sequentially (1, 2, 3, 4) with zero duplicates.
* **`RANK()`**: Competition ranking (1, 2, 2, 4) — leaves numerical gaps after ties.
* **`DENSE_RANK()`**: Category ranking (1, 2, 2, 3) — **leaves no gaps** after ties. Use this for "Top N Unique Values" problems like LeetCode 185.

### Rolling Calculations (`ROWS BETWEEN`)
When calculating moving metrics (such as a 7-day rolling average in LeetCode 1321), use:
`SUM(amount) OVER (ORDER BY visited_on ROWS BETWEEN 6 PRECEDING AND CURRENT ROW)`
This explicitly instructs SQL to look back at the last 6 calendar days plus the current day to calculate an accurate 7-day total.

### String Manipulation & Pattern Matching
Covers text operations like `CONCAT()`, `SUBSTRING()`, `LOWER()`, `UPPER()`, `TRIM()`, pattern matching with `LIKE`, and regular expression matching with `REGEXP`.

---

## ⚙️ SQL Execution Order Reminder

When writing and debugging queries, remember the order in which the database executes your code behind the scenes:

1. `FROM` / `JOIN` — Identifies and joins source tables.
2. `WHERE` — Filters raw rows before grouping or windowing (*Column aliases created in SELECT do not exist yet!*).
3. `GROUP BY` — Groups rows together.
4. `HAVING` — Filters grouped records.
5. `SELECT` — Calculates final columns, aliases, and window functions like `DENSE_RANK()`.
6. `DISTINCT` — Eliminates duplicate output rows.
7. `ORDER BY` — Sorts the final result set.
8. `LIMIT` — Restricts the total number of output rows.