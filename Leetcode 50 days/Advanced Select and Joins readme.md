# Advanced Select and Joins
# 1731. The Number of Employees Which Report to Each Employee

**Difficulty:** Easy  
**Topic:** Self-Joins & Aggregation

---

## Problem Statement

Table: `Employees`

| Column Name | Type |
| :--- | :--- |
| `employee_id` | int |
| `name` | varchar |
| `reports_to` | int |
| `age` | int |

* `employee_id` is the column with unique values for this table.
* `reports_to` contains the ID of the manager an employee reports to (or `null` if they do not report to anyone).

A **manager** is defined as an employee who has at least 1 other employee reporting directly to them.

Write a solution to report for all managers:
1. `employee_id`
2. `name`
3. `reports_count` (number of employees reporting directly to them)
4. `average_age` (average age of direct reports, **rounded to the nearest integer**)

Return the result table ordered by `employee_id` in **ascending order**.

---

## Example 1

### Input

`Employees` table:

| employee_id | name | reports_to | age |
| :--- | :--- | :--- | :--- |
| 9 | Hercy | null | 43 |
| 6 | Alice | 9 | 41 |
| 4 | Bob | 9 | 36 |
| 2 | Winston | null | 37 |

### Output

| employee_id | name | reports_count | average_age |
| :--- | :--- | :--- | :--- |
| 9 | Hercy | 2 | 39 |

### Explanation

* **Hercy (ID 9):** Has 2 direct reports (Alice, age 41, and Bob, age 36).  
  Average age = $(41 + 36) / 2 = 38.5 \rightarrow 39$ (rounded to the nearest integer).

---

## Example 2

### Input

`Employees` table:

| employee_id | name | reports_to | age |
| :--- | :--- | :--- | :--- |
| 1 | Michael | null | 45 |
| 2 | Alice | 1 | 38 |
| 3 | Bob | 1 | 42 |
| 4 | Charlie | 2 | 34 |
| 5 | David | 2 | 40 |
| 6 | Eve | 3 | 37 |
| 7 | Frank | null | 50 |
| 8 | Grace | null | 48 |

### Output

| employee_id | name | reports_count | average_age |
| :--- | :--- | :--- | :--- |
| 1 | Michael | 2 | 40 |
| 2 | Alice | 2 | 37 |
| 3 | Bob | 1 | 37 |

---

## Solution

```sql
SELECT e.employee_id, e.name, tb.reports_count, tb.average_age 
FROM Employees AS e
RIGHT JOIN (
    SELECT reports_to, COUNT(*) AS reports_count, ROUND(AVG(age)) AS average_age 
    FROM Employees 
    WHERE reports_to IS NOT NULL
    GROUP BY reports_to
) AS tb
ON e.employee_id = tb.reports_to
ORDER BY e.employee_id;
```
---

# 1789. Primary Department for Each Employee

**Difficulty:** Easy  
**Topic:** Joins, Aggregation & Conditional Logic

---

## Problem Statement

Table: `Employee`

| Column Name | Type |
| :--- | :--- |
| `employee_id` | int |
| `department_id` | int |
| `primary_flag` | varchar |

* `(employee_id, department_id)` is the primary key (combination of columns with unique values) for this table.
* `primary_flag` is an ENUM of type `('Y', 'N')`. If `'Y'`, it is the primary department for the employee.
* **Note:** When an employee belongs to **only one department**, their `primary_flag` is set to `'N'`.

Write a solution to report all employees with their primary department:
1. For employees belonging to multiple departments, select the department where `primary_flag = 'Y'`.
2. For employees belonging to only one department, report their only department.

Return the result table in **any order**.

---

## Example

### Input

`Employee` table:

| employee_id | department_id | primary_flag |
| :--- | :--- | :--- |
| 1 | 1 | N |
| 2 | 1 | Y |
| 2 | 2 | N |
| 3 | 3 | N |
| 4 | 2 | N |
| 4 | 3 | Y |
| 4 | 4 | N |

### Output

| employee_id | department_id |
| :--- | :--- |
| 1 | 1 |
| 2 | 1 |
| 3 | 3 |
| 4 | 3 |

### Explanation

* **Employee 1:** Belongs to 1 department $\rightarrow$ Department `1`.
* **Employee 2:** Belongs to 2 departments $\rightarrow$ Primary department (`'Y'`) is `1`.
* **Employee 3:** Belongs to 1 department $\rightarrow$ Department `3`.
* **Employee 4:** Belongs to 3 departments $\rightarrow$ Primary department (`'Y'`) is `3`.

---

## Solution

### Approach 1: Subquery with `LEFT JOIN`

```sql
SELECT e.employee_id, e.department_id FROM Employee AS e
LEFT JOIN (
    SELECT employee_id, COUNT(*) AS count FROM Employee
    GROUP BY employee_id
) AS tb
ON e.employee_id = tb.employee_id
WHERE tb.count = 1 OR (tb.count > 1 AND e.primary_flag = 'Y');
```
---
# 610. Triangle Judgement

**Difficulty:** Easy  
**Topic:** Conditional Statements (`CASE WHEN`)

---

## Problem Statement

Table: `Triangle`

| Column Name | Type |
| :--- | :--- |
| `x` | int |
| `y` | int |
| `z` | int |

* `(x, y, z)` is the primary key column for this table.
* Each row contains the lengths of three line segments (`x`, `y`, `z`).

Write a solution to report for every three line segments whether they can form a valid triangle (`'Yes'` or `'No'`).

Return the result table in **any order**.

---

## Example

### Input

`Triangle` table:

| x | y | z |
| :--- | :--- | :--- |
| 13 | 15 | 30 |
| 10 | 20 | 15 |

### Output

| x | y | z | triangle |
| :--- | :--- | :--- | :--- |
| 13 | 15 | 30 | No |
| 10 | 20 | 15 | Yes |

### Explanation

* **Row 1:** $13 + 15 = 28$, which is **not** greater than $30 \rightarrow$ Cannot form a triangle (`'No'`).
* **Row 2:** $10 + 20 > 15$, $10 + 15 > 20$, and $20 + 15 > 10 \rightarrow$ Forms a valid triangle (`'Yes'`).

---

## Solution

```sql SELECT x, y, z,
    CASE 
        WHEN x > 0 and y > 0 and z > 0 and
(x + y > z) and (x + z > y) and (y + z > x) then 'Yes'
        ELSE 'No'
    END AS triangle
FROM Triangle;
```
---
# 180. Consecutive Numbers

**Difficulty:** Medium  
**Topic:** Self-Joins & Window Functions

---

## Problem Statement

Table: `Logs`

| Column Name | Type |
| :--- | :--- |
| `id` | int |
| `num` | varchar |

* `id` is the primary key for this table.
* `id` is an autoincrement column starting from 1.

Find all numbers that appear at least **three times consecutively**.

Return the result table in **any order**.

---

## Example

### Input

`Logs` table:

| id | num |
| :--- | :--- |
| 1 | 1 |
| 2 | 1 |
| 3 | 1 |
| 4 | 2 |
| 5 | 1 |
| 6 | 2 |
| 7 | 2 |

### Output

| ConsecutiveNums |
| :--- |
| 1 |

### Explanation

`1` is the only number that appears consecutively for at least three times (at `id`s 1, 2, and 3).

---

## concepts
```sql
-- run this command to see the updated table
 select * from logs l1
 join logs l2 on l1.id = l2.id - 1
 join logs l3 on l1.id = l3.id - 2


-- method 2: using lead function
select num,
lead(num, 1) over (order by id) as next_1,
lead(num, 2) over (order by id) as next_2 from logs

-- lead(num, 1) -> this will go and find num value from the next row
-- lead(num, 2) -> this will go and find num value from the 2nd next row
```

## Solution

### Approach 1: Self-Join (ID Match)

```sql
SELECT DISTINCT l1.num AS ConsecutiveNums 
FROM Logs l1
JOIN Logs l2 ON l1.id = l2.id - 1
JOIN Logs l3 ON l1.id = l3.id - 2
WHERE l1.num = l2.num 
  AND l2.num = l3.num;
```

### Approach 2: Using Lead function
```sql
select distinct num as ConsecutiveNums from (

select num,
lead(num, 1) over (order by id) as next_1,
lead(num, 2) over (order by id) as next_2 from logs
) as tb
where num = next_1 and next_1 = next_2
```
---
# 1164. Product Price at a Given Date

**Difficulty:** Medium  
**Topic:** Subqueries, Joins & Conditional Logic

---

## Problem Statement

Table: `Products`

| Column Name | Type |
| :--- | :--- |
| `product_id` | int |
| `new_price` | int |
| `change_date` | date |

* `(product_id, change_date)` is the primary key (combination of columns with unique values) for this table.
* Each row indicates that the price of a product was changed to `new_price` on `change_date`.
* **Initially, all products have a price of 10.**

Write a solution to find the prices of all products on **2019-08-16**.

Return the result table in **any order**.

---

## Example

### Input

`Products` table:

| product_id | new_price | change_date |
| :--- | :--- | :--- |
| 1 | 20 | 2019-08-14 |
| 2 | 50 | 2019-08-14 |
| 1 | 30 | 2019-08-15 |
| 1 | 35 | 2019-08-16 |
| 2 | 65 | 2019-08-17 |
| 3 | 20 | 2019-08-18 |

### Output

| product_id | price |
| :--- | :--- |
| 2 | 50 |
| 1 | 35 |
| 3 | 10 |

### Explanation

* **Product 1:** Latest price change on or before `2019-08-16` is on `2019-08-16` $\rightarrow$ Price = `35`.
* **Product 2:** Price changed to `50` on `2019-08-14` and to `65` on `2019-08-17`. On `2019-08-16`, the active price was `50`.
* **Product 3:** First price change occurred after `2019-08-16` (on `2019-08-18`). Default initial price applies $\rightarrow$ Price = `10`.

---
## concept
```sql
select p.product_id,
-- if some id who came latter than 16-08-2019 then automatically set them 10 becasue there last_update will be null
case when tb.last_update is null then 10
else p.new_price end as price,
p.change_date, tb.last_update from products as p
left join (
--run this table first
select product_id, max(change_date) as last_update from products
where change_date <= '2019-08-16'
group by product_id
) as tb
on p.product_id = tb.product_id
where p.change_date = tb.last_update or last_update is null
-- if last_update null means that value came latter the update date
```

## Solution

```sql
select distinct p.product_id,
case when tb.last_update is null then 10
else p.new_price end as price
from products as p
left join (
select product_id, max(change_date) as last_update from products
where change_date <= '2019-08-16'
group by product_id
) as tb
on p.product_id = tb.product_id
where p.change_date = tb.last_update or last_update is null
```
---
# 1204. Last Person to Fit in the Bus

**Difficulty:** Medium  
**Topic:** Window Functions, Running Sums & Sorting

---

## Problem Statement

Table: `Queue`

| Column Name | Type |
| :--- | :--- |
| `person_id` | int |
| `person_name` | varchar |
| `weight` | int |
| `turn` | int |

* `person_id` contains unique values.
* `turn` determines the boarding sequence, where `turn = 1` is first and `turn = n` is last.
* `weight` is the weight of the person in kilograms.
* The bus has a maximum weight capacity of **1000 kilograms**.

Write a solution to find the `person_name` of the **last person** who can fit on the bus without exceeding the weight limit.

---

## Example

### Input

`Queue` table:

| person_id | person_name | weight | turn |
| :--- | :--- | :--- | :--- |
| 5 | Alice | 250 | 1 |
| 4 | Bob | 175 | 5 |
| 3 | Alex | 350 | 2 |
| 6 | John Cena | 400 | 3 |
| 1 | Winston | 500 | 6 |
| 2 | Marie | 200 | 4 |

### Output

| person_name |
| :--- |
| John Cena |

### Explanation

Boarding order sorted by `turn`:

| Turn | ID | Name | Weight | Total Weight | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 5 | Alice | 250 | 250 | Boards |
| 2 | 3 | Alex | 350 | 600 | Boards |
| 3 | 6 | John Cena | 400 | 1000 | **Last person to board** |
| 4 | 2 | Marie | 200 | 1200 | Cannot board |
| 5 | 4 | Bob | 175 | 1375 | Cannot board |
| 6 | 1 | Winston | 500 | 1875 | Cannot board |

---
## Concept
```sql
select person_id, turn, person_name, weight, total_weight
from (

select person_id, turn, person_name, weight,
-- we performed here cumulative sum or row wise sum. over operation is used to perform row wise something
sum(weight) over (order by turn) as total_weight from queue
order by turn asc
) as tb
-- filter out the total_weight values that is less than 1000
where total_weight <= 1000
-- order it in descending order with 1 limit value
order by total_weight desc limit 1
```


## Solution

```sql
SELECT person_name
FROM (
    SELECT person_id, turn, person_name, weight,
        SUM(weight) OVER (ORDER BY turn) AS total_weight 
    FROM queue
    ORDER BY turn ASC
) AS tb
WHERE total_weight <= 1000
ORDER BY total_weight DESC 
LIMIT 1;
```
---
# 1907. Count Salary Categories

**Difficulty:** Medium  
**Topic:** CTEs, Joins & Conditional Categorization

---

## Problem Statement

Table: `Accounts`

| Column Name | Type |
| :--- | :--- |
| `account_id` | int |
| `income` | int |

* `account_id` is the primary key (column with unique values) for this table.
* Each row contains information about the monthly income for one bank account.

Write a solution to calculate the number of bank accounts for each salary category:
* **"Low Salary":** All salaries **strictly less than** $20,000.
* **"Average Salary":** All salaries in the **inclusive range** [$20,000, $50,000].
* **"High Salary":** All salaries **strictly greater than** $50,000.

The result table **must contain all three categories**. If there are no accounts in a category, return `0`.

Return the result table in **any order**.

---

## Example

### Input

`Accounts` table:

| account_id | income |
| :--- | :--- |
| 3 | 108939 |
| 2 | 12747 |
| 8 | 87709 |
| 6 | 91796 |

### Output

| category | accounts_count |
| :--- | :--- |
| Low Salary | 1 |
| Average Salary | 0 |
| High Salary | 3 |

### Explanation

* **Low Salary:** Account 2 (income = 12747) $\rightarrow$ Count = 1
* **Average Salary:** No accounts fit this range $\rightarrow$ Count = 0
* **High Salary:** Accounts 3, 6, and 8 (income > 50000) $\rightarrow$ Count = 3

---

## Concept
```sql
-- here we have to use with statement which is CTE
-- with is used to create a new table
with categoriesTable as (
    -- I want to create a single column tabel that will contain
    select 'Low Salary' as category
    union all
    select 'Average Salary' as category
    union all
    select 'High Salary' as category
),
currentTable as (
    select account_id,
    case when income < 20000 then 'Low Salary'
    when income between 20000 and 50000 then 'Average Salary'
    when income > 50000 then 'High Salary' end as category
    from accounts
)

-- it actually creating two tables name currentTable and categoriesTable
-- then by using them we can perform something
```

## Solution

```sql
with categoriesTable as (
    select 'Low Salary' as category
    union all
    select 'Average Salary' as category
    union all
    select 'High Salary' as category
),
currentTable as (
    select account_id,
    case when income < 20000 then 'Low Salary'
    when income between 20000 and 50000 then 'Average Salary'
    when income > 50000 then 'High Salary' end as category
    from accounts
)
-- select * from currentTable
-- select * from categoriesTable
select c.category, count(cr.account_id) as accounts_count from categoriesTable c
left join currentTable as cr on
c.category = cr.category
group by c.category
```
---