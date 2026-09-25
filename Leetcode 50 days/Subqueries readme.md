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