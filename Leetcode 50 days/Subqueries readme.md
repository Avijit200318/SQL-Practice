# Subqueries
# 1978. Employees Whose Manager Left the Company

**Difficulty:** Easy  
**Topic:** Subqueries & Filtering

---

## Problem Statement

Table: `Employees`

| Column Name | Type |
| :--- | :--- |
| `employee_id` | int |
| `name` | varchar |
| `manager_id` | int |
| `salary` | int |

* `employee_id` is the primary key for this table.
* Each row contains information about an employee, their salary, and the ID of their manager (`manager_id`).
* Some employees do not have a manager (`manager_id` is `null`).

Find the IDs of the employees whose **salary is strictly less than $30,000** and whose **manager left the company**.

When a manager leaves the company, their record is deleted from the `Employees` table, but their former direct reports still retain their `manager_id`.

Return the result table ordered by `employee_id` in **ascending order**.

---

## Example

### Input

`Employees` table:

| employee_id | name | manager_id | salary |
| :--- | :--- | :--- | :--- |
| 3 | Mila | 9 | 60301 |
| 12 | Antonella | null | 31000 |
| 13 | Emery | null | 67084 |
| 1 | Kalel | 11 | 21241 |
| 9 | Mikaela | null | 50937 |
| 11 | Joziah | 6 | 28485 |

### Output

| employee_id |
| :--- |
| 11 |

### Explanation

* Employees with a salary $< \$30,000$: Employee `1` (Kalel, \$21,241) and Employee `11` (Joziah, \$28,485).
* **Employee 1 (Kalel):** `manager_id` is `11`. Employee `11` exists in the table $\rightarrow$ Manager is present.
* **Employee 11 (Joziah):** `manager_id` is `6`. Employee `6` does not exist in the table $\rightarrow$ Manager left the company.

---

## Solution

```sql
-- run this first line with the entire code then change it with second line
-- select employee_id, name, manager_id, salary from employees
select employee_id from employees
where salary < 30000 and
manager_id not in (select employee_id from employees)
order by employee_id asc
```
---
# 626. Exchange Seats

**Difficulty:** Medium  
**Topic:** Conditional Logic (`CASE WHEN`) & Modular Arithmetic

---

## Problem Statement

Table: `Seat`

| Column Name | Type |
| :--- | :--- |
| `id` | int |
| `student` | varchar |

* `id` is the primary key (unique value) column for this table.
* The `id` sequence always starts from 1 and increments continuously.
* Each row contains the name and ID of a student.

Write a solution to **swap the seat ID of every two consecutive students**.  
If the number of students is odd, the ID of the last student is not swapped.

Return the result table ordered by `id` in **ascending order**.

---

## Example

### Input

`Seat` table:

| id | student |
| :--- | :--- |
| 1 | Abbot |
| 2 | Doris |
| 3 | Emerson |
| 4 | Green |
| 5 | Jeames |

### Output

| id | student |
| :--- | :--- |
| 1 | Doris |
| 2 | Abbot |
| 3 | Green |
| 4 | Emerson |
| 5 | Jeames |

### Explanation

* **ID 1 (Odd):** Swapped with ID 2 $\rightarrow$ New ID = 2 (`Doris`)
* **ID 2 (Even):** Swapped with ID 1 $\rightarrow$ New ID = 1 (`Abbot`)
* **ID 3 (Odd):** Swapped with ID 4 $\rightarrow$ New ID = 4 (`Green`)
* **ID 4 (Even):** Swapped with ID 3 $\rightarrow$ New ID = 3 (`Emerson`)
* **ID 5 (Odd & Last):** No consecutive partner $\rightarrow$ Remains ID 5 (`Jeames`)

---

## Solution

```sql
# Write your MySQL query statement below
select case
    when id % 2 = 1 and id = (select max(id) from seat) then id
    when id % 2 = 1 then id + 1
    else id - 1
    end as id,
    student from seat
    order by id asc
```
---
# 1341. Movie Rating

**Difficulty:** Medium  
**Topic:** Joins, Aggregation, Date Filtering & CTEs

---

## Problem Statement

Table: `Movies`

| Column Name | Type |
| :--- | :--- |
| `movie_id` | int |
| `title` | varchar |

* `movie_id` is the primary key (column with unique values) for this table.
* Each movie has a unique title.

Table: `Users`

| Column Name | Type |
| :--- | :--- |
| `user_id` | int |
| `name` | varchar |

* `user_id` is the primary key (column with unique values) for this table.
* `name` has unique values.

Table: `MovieRating`

| Column Name | Type |
| :--- | :--- |
| `movie_id` | int |
| `user_id` | int |
| `rating` | int |
| `created_at` | date |

* `(movie_id, user_id)` is the primary key (column with unique values) for this table.

Write a solution to:
1. Find the name of the user who has rated the **greatest number of movies**. In case of a tie, return the lexicographically smaller user name.
2. Find the movie name with the **highest average rating in February 2020**. In case of a tie, return the lexicographically smaller movie name.

---

## Example

### Input

`Movies` table:

| movie_id | title |
| :--- | :--- |
| 1 | Avengers |
| 2 | Frozen 2 |
| 3 | Joker |

`Users` table:

| user_id | name |
| :--- | :--- |
| 1 | Daniel |
| 2 | Monica |
| 3 | Maria |
| 4 | James |

`MovieRating` table:

| movie_id | user_id | rating | created_at |
| :--- | :--- | :--- | :--- |
| 1 | 1 | 3 | 2020-01-12 |
| 1 | 2 | 4 | 2020-02-11 |
| 1 | 3 | 2 | 2020-02-12 |
| 1 | 4 | 1 | 2020-01-01 |
| 2 | 1 | 5 | 2020-02-17 |
| 2 | 2 | 2 | 2020-02-01 |
| 2 | 3 | 2 | 2020-03-01 |
| 3 | 1 | 3 | 2020-02-22 |
| 3 | 2 | 4 | 2020-02-25 |

### Output

| results |
| :--- |
| Daniel |
| Frozen 2 |

### Explanation

* **User with most ratings:** Daniel and Monica both rated 3 movies. "Daniel" is lexicographically smaller than "Monica", so Daniel is selected.
* **Highest rated movie in Feb 2020:** Frozen 2 and Joker both have an average rating of 3.5 in February. "Frozen 2" is lexicographically smaller than "Joker", so Frozen 2 is selected.

---

## Solution

```sql
-- create a full joined table
with fullTable as (
select mr.movie_id, m.title, mr.user_id, u.name, mr.rating, mr.created_at from movierating as mr
left join users u on mr.user_id = u.user_id
left join movies as m on mr.movie_id = m.movie_id
)

-- I used this full joined table to filter out the results

select name as results from (

select user_id, count(*) as user_count, name from fullTable
group by user_id
order by user_count desc, name asc limit 1
)as tb

-- if we use normal union then it will remove duplicate values if we want ot keep them use union all
union all

select title as results from (

select movie_id, title, rating, created_at, (sum(rating)/count(*)) as avg_rating from fullTable
where Date_format(created_at, '%Y-%m') = '2020-02'
group by movie_id
order by avg_rating desc, title asc limit 1
) as tb2
```
---
# 1321. Restaurant Growth

**Difficulty:** Medium  
**Topic:** Window Functions, Rolling Aggregations & Date Functions

---

## Problem Statement

Table: `Customer`

| Column Name | Type |
| :--- | :--- |
| `customer_id` | int |
| `name` | varchar |
| `visited_on` | date |
| `amount` | int |

* `(customer_id, visited_on)` is the primary key for this table.
* Each row contains data about customer transactions in a restaurant.
* `visited_on` is the date on which a customer visited, and `amount` is the amount paid.

Compute the **moving average** of how much customers paid in a **7-day window** (current day + 6 days before).

* `average_amount` should be **rounded to 2 decimal places**.
* The output should only include dates that have a full 7-day window.

Return the result table ordered by `visited_on` in **ascending order**.

---

## Example

### Input

`Customer` table:

| customer_id | name | visited_on | amount |
| :--- | :--- | :--- | :--- |
| 1 | Jhon | 2019-01-01 | 100 |
| 2 | Daniel | 2019-01-02 | 110 |
| 3 | Jade | 2019-01-03 | 120 |
| 4 | Khaled | 2019-01-04 | 130 |
| 5 | Winston | 2019-01-05 | 110 |
| 6 | Elvis | 2019-01-06 | 140 |
| 7 | Anna | 2019-01-07 | 150 |
| 8 | Maria | 2019-01-08 | 80 |
| 9 | Jaze | 2019-01-09 | 110 |
| 1 | Jhon | 2019-01-10 | 130 |
| 3 | Jade | 2019-01-10 | 150 |

### Output

| visited_on | amount | average_amount |
| :--- | :--- | :--- |
| 2019-01-07 | 860 | 122.86 |
| 2019-01-08 | 840 | 120 |
| 2019-01-09 | 840 | 120 |
| 2019-01-10 | 1000 | 142.86 |

### Explanation

* **1st 7-day window (2019-01-01 to 2019-01-07):**
  * Total = $100 + 110 + 120 + 130 + 110 + 140 + 150 = 860$
  * Average = $860 / 7 = 122.86$
* **2nd 7-day window (2019-01-02 to 2019-01-08):**
  * Total = $110 + 120 + 130 + 110 + 140 + 150 + 80 = 840$
  * Average = $840 / 7 = 120$
* **3rd 7-day window (2019-01-03 to 2019-01-09):**
  * Total = $120 + 130 + 110 + 140 + 150 + 80 + 110 = 840$
  * Average = $840 / 7 = 120$
* **4th 7-day window (2019-01-04 to 2019-01-10):**
  * Total = $130 + 110 + 140 + 150 + 80 + 110 + 130 + 150 = 1000$
  * Average = $1000 / 7 = 142.86$

---

## Solution

```sql
# Write your MySQL query statement below
-- 1. each day total amount
-- 2. we need to sum up 7 rows together

with dailyAmount as (
    select visited_on, sum(amount) as amount from customer
    group by visited_on
),
-- this table will contain seven day total amount and avg
sevenDayTable as (
    select visited_on,
    -- we are performing cumulative sum of 6 rows with including the current one
    sum(amount) over (
        order by visited_on asc
        rows between 6 preceding and current row
        ) as amount,
    round(avg(amount) over (
        order by visited_on asc
        rows between 6 preceding and current row
    ), 2) as avg
    from dailyAmount
    -- instead of customer table we are using the dailyAmount table
)

select visited_on, amount, avg as average_amount from sevenDayTable
where Datediff(visited_on, (select min(visited_on) from customer)) >= 6
-- if the date difference is greather than equal to 6 then show them
```
---
# 602. Friend Requests II: Who Has the Most Friends

**Difficulty:** Medium  
**Topic:** CTEs, Aggregation & Combined Query Sets (`UNION ALL`)

---

## Problem Statement

Table: `RequestAccepted`

| Column Name | Type |
| :--- | :--- |
| `requester_id` | int |
| `accepter_id` | int |
| `accept_date` | date |

* `(requester_id, accepter_id)` is the primary key (combination of columns with unique values) for this table.
* This table contains the ID of the user who sent the friend request, the ID of the user who accepted it, and the acceptance date.

Write a solution to find the person who has the **most friends** and their total **number of friends**.

The test cases are generated such that only one person has the most friends.

---

## Example

### Input

`RequestAccepted` table:

| requester_id | accepter_id | accept_date |
| :--- | :--- | :--- |
| 1 | 2 | 2016/06/03 |
| 1 | 3 | 2016/06/08 |
| 2 | 3 | 2016/06/08 |
| 3 | 4 | 2016/06/09 |

### Output

| id | num |
| :--- | :--- |
| 3 | 3 |

### Explanation

* **User 1:** Friends with `2` and `3` $\rightarrow$ 2 friends
* **User 2:** Friends with `1` and `3` $\rightarrow$ 2 friends
* **User 3:** Friends with `1`, `2`, and `4` $\rightarrow$ 3 friends
* **User 4:** Friends with `3` $\rightarrow$ 1 friend

User `3` has the most friends (3 friends total).

---

## Solution

```sql
-- separate the tables first request table with each id count and acceptable with each id count

with rquestTable as (
    select requester_id as id, count(*) as total from requestAccepted
    group by requester_id
),
acceptTable as (
    select accepter_id as id, count(*) as total from requestAccepted
    group by accepter_id
),
-- merge both the table then we can count the total id wise
fullTable as (
    select * from rquestTable
    union all
    select * from acceptTable
)

select id, sum(total) as num from fullTable
group by id
order by num desc limit 1
```
---
# 585. Investments in 2016

**Difficulty:** Medium  
**Topic:** Subqueries, Aggregation & Grouping (`HAVING`)

---

## Problem Statement

Table: `Insurance`

| Column Name | Type |
| :--- | :--- |
| `pid` | int |
| `tiv_2015` | float |
| `tiv_2016` | float |
| `lat` | float |
| `lon` | float |

* `pid` is the primary key (column with unique values) for this table.
* `tiv_2015` is the total investment value in 2015, and `tiv_2016` is the total investment value in 2016.
* `(lat, lon)` represent the geographic latitude and longitude of the policyholder's city. Neither is `NULL`.

Write a solution to report the sum of all total investment values in 2016 (`tiv_2016`) for all policyholders who meet **both** criteria:
1. Have the same `tiv_2015` value as one or more other policyholders.
2. Are located at a unique `(lat, lon)` pair (i.e., not located in the same city as any other policyholder).

Round `tiv_2016` to **two decimal places**.

---

## Example

### Input

`Insurance` table:

| pid | tiv_2015 | tiv_2016 | lat | lon |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 10 | 5 | 10 | 10 |
| 2 | 20 | 20 | 20 | 20 |
| 3 | 10 | 30 | 20 | 20 |
| 4 | 10 | 40 | 40 | 40 |

### Output

| tiv_2016 |
| :--- |
| 45.00 |

### Explanation

* **Policy 1 (`pid = 1`):** `tiv_2015` = 10 (shared with `pid` 3 & 4) and `(lat, lon)` = (10, 10) (unique location) $\rightarrow$ **Valid**
* **Policy 2 (`pid = 2`):** `tiv_2015` = 20 (unique) and `(lat, lon)` = (20, 20) (shared with `pid` 3) $\rightarrow$ Invalid
* **Policy 3 (`pid = 3`):** `tiv_2015` = 10 (shared), but `(lat, lon)` = (20, 20) (shared with `pid` 2) $\rightarrow$ Invalid
* **Policy 4 (`pid = 4`):** `tiv_2015` = 10 (shared) and `(lat, lon)` = (40, 40) (unique location) $\rightarrow$ **Valid**

Sum of `tiv_2016` for valid policies (1 & 4) = $5 + 40 = 45.00$.

---

## Solution

```sql
# Write your MySQL query statement below
with tb as (
    select * from insurance
    -- first condition
    where tiv_2015 in (
        select tiv_2015 from insurance
        group by tiv_2015 having count(*) > 1
    )
    -- second condition
    and (lat, lon) in (
        select lat, lon from insurance
        group by lat, lon having count(*) = 1
    )
)

select round( sum(tiv_2016), 2) as tiv_2016 from tb
```
---
# 185. Department Top Three Salaries

**Difficulty:** Hard  
**Topic:** Window Functions (`DENSE_RANK`), Joins & CTEs

---

## Problem Statement

Table: `Employee`

| Column Name | Type |
| :--- | :--- |
| `id` | int |
| `name` | varchar |
| `salary` | int |
| `departmentId` | int |

* `id` is the primary key (column with unique values) for this table.
* `departmentId` is a foreign key referencing the `Department` table.
* Each row indicates the ID, name, salary, and department ID of an employee.

Table: `Department`

| Column Name | Type |
| :--- | :--- |
| `id` | int |
| `name` | varchar |

* `id` is the primary key (column with unique values) for this table.
* Each row indicates the ID and name of a department.

A **high earner** in a department is defined as an employee whose salary is in the **top three unique salaries** for that department.

Write a solution to find all high earners in each department.

Return the result table in **any order**.

---

## Example

### Input

`Employee` table:

| id | name | salary | departmentId |
| :--- | :--- | :--- | :--- |
| 1 | Joe | 85000 | 1 |
| 2 | Henry | 80000 | 2 |
| 3 | Sam | 60000 | 2 |
| 4 | Max | 90000 | 1 |
| 5 | Janet | 69000 | 1 |
| 6 | Randy | 85000 | 1 |
| 7 | Will | 70000 | 1 |

`Department` table:

| id | name |
| :--- | :--- |
| 1 | IT |
| 2 | Sales |

### Output

| Department | Employee | Salary |
| :--- | :--- | :--- |
| IT | Max | 90000 |
| IT | Joe | 85000 |
| IT | Randy | 85000 |
| IT | Will | 70000 |
| Sales | Henry | 80000 |
| Sales | Sam | 60000 |

### Explanation

* **IT Department:**
  * Highest salary: `90000` (Max) $\rightarrow$ Rank 1
  * 2nd highest unique salary: `85000` (Joe & Randy) $\rightarrow$ Rank 2 (both included)
  * 3rd highest unique salary: `70000` (Will) $\rightarrow$ Rank 3
  * `69000` (Janet) $\rightarrow$ Rank 4 (excluded)
* **Sales Department:**
  * Highest salary: `80000` (Henry) $\rightarrow$ Rank 1
  * 2nd highest salary: `60000` (Sam) $\rightarrow$ Rank 2

---

## Solution

```sql
# Write your MySQL query statement below
with tb as (
    select e.id, e.name, e.salary, e.departmentId, d.name as department from employee as e
    left join department as d on e.departmentId = d.id
)

-- select id, name, department, salary,
-- dense_rank() over (
--     partition by department
--     order by salary desc
-- ) as salary_rank
-- from tb

-- **** check this code first before the bellow code ***


-- *** where clasuse run first before select: so hwere if we use the innner thing just and use where clause then it will not find the salary_rank column ***

select department, name as employee, salary from (

select id, name, department, salary,
-- rank() dense_rank() is used to rank the vaule check them first
-- dense_rank() will give us unique rank value
dense_rank() over (
  -- we are paritioning the table based on department
    partition by department
    order by salary desc
) as salary_rank
from tb
) as t
where salary_rank <= 3
```
---