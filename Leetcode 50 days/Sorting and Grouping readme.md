# Sorting and Grouping
# 2356. Number of Unique Subjects Taught by Each Teacher

**Difficulty:** Easy  
**Topic:** Aggregation & Grouping

---

## Problem Statement

Table: `Teacher`

| Column Name | Type |
| :--- | :--- |
| `teacher_id` | int |
| `subject_id` | int |
| `dept_id` | int |

* `(subject_id, dept_id)` is the primary key (combination of columns with unique values) of this table.
* Each row indicates that a teacher with `teacher_id` teaches `subject_id` in `dept_id`.

Write a solution to calculate the number of **unique subjects** each teacher teaches in the university.  
Return the result table in **any order**.

---

## Example

### Input

`Teacher` table:

| teacher_id | subject_id | dept_id |
| :--- | :--- | :--- |
| 1 | 2 | 3 |
| 1 | 2 | 4 |
| 1 | 3 | 3 |
| 2 | 1 | 1 |
| 2 | 2 | 1 |
| 2 | 3 | 1 |
| 2 | 4 | 1 |

### Output

| teacher_id | cnt |
| :--- | :--- |
| 1 | 2 |
| 2 | 4 |

### Explanation

* **Teacher 1:** Teaches `subject_id` 2 (in departments 3 & 4) and `subject_id` 3 (in department 3). Distinct subjects taught = `2`.
* **Teacher 2:** Teaches `subject_id` 1, 2, 3, and 4 (all in department 1). Distinct subjects taught = `4`.

---

## Solution

```sql
SELECT teacher_id, COUNT(DISTINCT subject_id) AS cnt 
FROM teacher
GROUP BY teacher_id;
```
---

# 1141. User Activity for the Past 30 Days I

**Difficulty:** Easy  
**Topic:** Filtering & Date Range Aggregation

---

## Problem Statement

Table: `Activity`

| Column Name | Type |
| :--- | :--- |
| `user_id` | int |
| `session_id` | int |
| `activity_date` | date |
| `activity_type` | enum |

* This table may contain duplicate rows.
* `activity_type` is an enum of `('open_session', 'end_session', 'scroll_down', 'send_message')`.
* Each session belongs to exactly one user.

Write a solution to find the daily active user count for a period of **30 days ending 2019-07-27 inclusively** (i.e., from `2019-06-28` to `2019-07-27`). A user is considered active on a day if they performed at least one activity on that day.

Return the result table in **any order**.

---

## Example

### Input

`Activity` table:

| user_id | session_id | activity_date | activity_type |
| :--- | :--- | :--- | :--- |
| 1 | 1 | 2019-07-20 | open_session |
| 1 | 1 | 2019-07-20 | scroll_down |
| 1 | 1 | 2019-07-20 | end_session |
| 2 | 4 | 2019-07-20 | open_session |
| 2 | 4 | 2019-07-21 | send_message |
| 2 | 4 | 2019-07-21 | end_session |
| 3 | 2 | 2019-07-21 | open_session |
| 3 | 2 | 2019-07-21 | send_message |
| 3 | 2 | 2019-07-21 | end_session |
| 4 | 3 | 2019-06-25 | open_session |
| 4 | 3 | 2019-06-25 | end_session |

### Output

| day | active_users |
| :--- | :--- |
| 2019-07-20 | 2 |
| 2019-07-21 | 2 |

### Explanation

* **2019-07-20:** Users `1` and `2` were active $\rightarrow$ 2 active users.
* **2019-07-21:** Users `2` and `3` were active $\rightarrow$ 2 active users.
* **2019-06-25:** Activity on this date is excluded because it falls outside the 30-day window (`2019-06-28` to `2019-07-27`).

---

## Solution

```sql
SELECT activity_date AS day, COUNT(DISTINCT user_id) AS active_users 
FROM activity
WHERE activity_date BETWEEN '2019-06-28' AND '2019-07-27'
GROUP BY activity_date;
```
---
# 1070. Product Sales Analysis III

**Difficulty:** Medium  
**Topic:** Subqueries & Pair Matching

---

## Problem Statement

Table: `Sales`

| Column Name | Type |
| :--- | :--- |
| `sale_id` | int |
| `product_id` | int |
| `year` | int |
| `quantity` | int |
| `price` | int |

* `(sale_id, year)` is the primary key (combination of columns with unique values) of this table.
* Each row records a sale of a product in a given year.
* A product may have multiple sales entries in the same year.

Write a solution to select all sales entries that occurred in the **first year** each product was sold.  
Identify the earliest `year` for each `product_id` and return all corresponding records with columns: `product_id`, `first_year`, `quantity`, and `price`.

Return the result table in **any order**.

---

## Example

### Input

`Sales` table:

| sale_id | product_id | year | quantity | price |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 100 | 2008 | 10 | 5000 |
| 2 | 100 | 2009 | 12 | 5000 |
| 7 | 200 | 2011 | 15 | 9000 |

### Output

| product_id | first_year | quantity | price |
| :--- | :--- | :--- | :--- |
| 100 | 2008 | 10 | 5000 |
| 200 | 2011 | 15 | 9000 |

### Explanation

* **Product 100:** First sold in year `2008`. The sale record for 2008 is included, while 2009 is ignored.
* **Product 200:** First sold in year `2011`. The sale record for 2011 is included.

---
## **Notes**
```sql
group by product_id having year = min(year)
--in this case if the id have years like 2008, 2009, 2010
-- min(year) will be 2008. But during comparision it may happend
-- it compare 2008 with 2009 or 2010 randomly then it will print nothing

where year = min(year)
-- we cannot write it like this becasue min(year) is aggregate function like a loop. So we have to write it like
where year = (select min(year) from sales)
```

## concept
```sql
-- the inner talbe will gave us only the product id, first year
-- by using that we can filter the original table sales
-- we will use both the product_id and year to filter from this table and show all the values
```

## Solution

```sql
SELECT product_id, year AS first_year, quantity, price
FROM sales 
WHERE (product_id, year) IN (
    SELECT product_id, MIN(year) AS first_year 
    FROM sales
    GROUP BY product_id
);
```
---
# 596. Classes More Than 5 Students

**Difficulty:** Easy  
**Topic:** Aggregation & Filtering Groups

---

## Problem Statement

Table: `Courses`

| Column Name | Type |
| :--- | :--- |
| `student` | varchar |
| `class` | varchar |

* `(student, class)` is the primary key (combination of columns with unique values) for this table.
* Each row indicates the name of a student and the class in which they are enrolled.

Write a solution to find all the classes that have **at least five students**.  
Return the result table in **any order**.

---

## Example

### Input

`Courses` table:

| student | class |
| :--- | :--- |
| A | Math |
| B | English |
| C | Math |
| D | Biology |
| E | Math |
| F | Computer |
| G | Math |
| H | Math |
| I | Math |

### Output

| class |
| :--- |
| Math |

### Explanation

* **Math:** 6 students $\rightarrow$ Included ($\ge 5$).
* **English:** 1 student $\rightarrow$ Excluded.
* **Biology:** 1 student $\rightarrow$ Excluded.
* **Computer:** 1 student $\rightarrow$ Excluded.

---

## Solution

```sql
SELECT class FROM courses
GROUP BY class 
HAVING COUNT(*) >= 5;
```
---
# 1729. Find Followers Count

**Difficulty:** Easy  
**Topic:** Aggregation & Sorting

---

## Problem Statement

Table: `Followers`

| Column Name | Type |
| :--- | :--- |
| `user_id` | int |
| `follower_id` | int |

* `(user_id, follower_id)` is the primary key (combination of columns with unique values) for this table.
* Each row contains the ID of a user and the ID of a follower following that user.

Write a solution to return the number of followers for each user.  
Return the result table ordered by `user_id` in **ascending order**.

---

## Example

### Input

`Followers` table:

| user_id | follower_id |
| :--- | :--- |
| 0 | 1 |
| 1 | 0 |
| 2 | 0 |
| 2 | 1 |

### Output

| user_id | followers_count |
| :--- | :--- |
| 0 | 1 |
| 1 | 1 |
| 2 | 2 |

### Explanation

* **User 0:** Followed by `{1}` $\rightarrow$ Count = 1
* **User 1:** Followed by `{0}` $\rightarrow$ Count = 1
* **User 2:** Followed by `{0, 1}` $\rightarrow$ Count = 2

---

## Solution

```sql
SELECT user_id, COUNT(*) AS followers_count FROM followers
GROUP BY user_id
ORDER BY user_id ASC;
```
---

# 619. Biggest Single Number

**Difficulty:** Easy  
**Topic:** Aggregation, Filtering Groups & Subqueries

---

## Problem Statement

Table: `MyNumbers`

| Column Name | Type |
| :--- | :--- |
| `num` | int |

* This table may contain duplicates (no primary key).
* Each row contains an integer.

A **single number** is a number that appears only once in the `MyNumbers` table.  
Write a solution to find the largest single number. If no single number exists, return `null`.

---

## Example 1

### Input

`MyNumbers` table:

| num |
| :--- |
| 8 |
| 8 |
| 3 |
| 3 |
| 1 |
| 4 |
| 5 |
| 6 |

### Output

| num |
| :--- |
| 6 |

### Explanation

The numbers appearing exactly once are `1`, `4`, `5`, and `6`. The largest among them is `6`.

---

## Example 2

### Input

`MyNumbers` table:

| num |
| :--- |
| 8 |
| 8 |
| 7 |
| 7 |
| 3 |
| 3 |
| 3 |

### Output

| num |
| :--- |
| null |

### Explanation

There are no numbers that appear only once in the table, so the result is `null`.

---

## Solution

```sql
select ifnull(
(
select num from mynumbers
group by num having count(num) = 1
order by num desc limit 1
), null
) as num
```
---
# 1045. Customers Who Bought All Products

**Difficulty:** Medium  
**Topic:** Aggregation & Subqueries

---

## Problem Statement

Table: `Customer`

| Column Name | Type |
| :--- | :--- |
| `customer_id` | int |
| `product_key` | int |

* This table may contain duplicate rows. `customer_id` is not `NULL`.
* `product_key` is a foreign key referencing the `Product` table.

Table: `Product`

| Column Name | Type |
| :--- | :--- |
| `product_key` | int |

* `product_key` is the primary key (column with unique values) for this table.

Write a solution to report the `customer_id`s from the `Customer` table that bought **all** the products listed in the `Product` table.

Return the result table in **any order**.

---

## Example

### Input

`Customer` table:

| customer_id | product_key |
| :--- | :--- |
| 1 | 5 |
| 2 | 6 |
| 3 | 5 |
| 3 | 6 |
| 1 | 6 |

`Product` table:

| product_key |
| :--- |
| 5 |
| 6 |

### Output

| customer_id |
| :--- |
| 1 |
| 3 |

### Explanation

The products available are `5` and `6` (Total count = 2).
* **Customer 1:** Bought products `5` and `6` (2 distinct products) $\rightarrow$ **Included**
* **Customer 2:** Bought product `6` (1 distinct product) $\rightarrow$ Excluded
* **Customer 3:** Bought products `5` and `6` (2 distinct products) $\rightarrow$ **Included**

---

## Concept:
```sql
select customer_id, count(distinct product_key) as total_product from customer
group by customer_id


--- From this table we can find each customer_id with number of distinct items they actually buy
--- and from the product table we can find how many product there is present
--- we can group the table using user id and calculate number of unique product they buy and then filter it using total number of product in the product table
```

## Solution

```sql
SELECT customer_id FROM Customer
GROUP BY customer_id
HAVING COUNT(DISTINCT product_key) = (SELECT COUNT(*) FROM Product);
```
---