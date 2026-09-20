# Basic aggregates function
# 620. Not Boring Movies

**Difficulty:** Easy  
**Topic:** Filtering & Sorting

---

## Problem Statement

Table: `Cinema`

| Column Name | Type |
| :--- | :--- |
| `id` | int |
| `movie` | varchar |
| `description` | varchar |
| `rating` | float |

* `id` is the primary key for this table.
* Each row contains information about the name of a movie, its genre, and its rating (2 decimal float in range `[0, 10]`).

Write a solution to report the movies with an **odd-numbered ID** and a **description that is not `"boring"`**.  
Return the result table ordered by `rating` in **descending order**.

---

## Example

### Input

`Cinema` table:

| id | movie | description | rating |
| :--- | :--- | :--- | :--- |
| 1 | War | great 3D | 8.9 |
| 2 | Science | fiction | 8.5 |
| 3 | irish | boring | 6.2 |
| 4 | Ice song | Fantacy | 8.6 |
| 5 | House card | Interesting | 9.1 |

### Output

| id | movie | description | rating |
| :--- | :--- | :--- | :--- |
| 5 | House card | Interesting | 9.1 |
| 1 | War | great 3D | 8.9 |

### Explanation

We have three movies with odd-numbered IDs: `1`, `3`, and `5`. The movie with `ID = 3` is "boring", so we exclude it. Sorting the remaining rows (`5` and `1`) by `rating` in descending order places `House card` (9.1) first and `War` (8.9) second.

---

## Solution

```sql
SELECT id, movie, description, rating 
FROM cinema
WHERE description != 'boring' 
  AND id % 2 != 0
ORDER BY rating DESC;
```
---
# 1251. Average Selling Price

**Difficulty:** Easy  
**Topic:** Joins, Aggregation & Conditional Functions

---

## Problem Statement

Table: `Prices`

| Column Name | Type |
| :--- | :--- |
| `product_id` | int |
| `start_date` | date |
| `end_date` | date |
| `price` | int |

* `(product_id, start_date, end_date)` is the primary key (combination of columns with unique values) for this table.
* Each row indicates the price of `product_id` for the period from `start_date` to `end_date`.
* For each `product_id`, there are no overlapping date ranges.

Table: `UnitsSold`

| Column Name | Type |
| :--- | :--- |
| `product_id` | int |
| `purchase_date` | date |
| `units` | int |

* This table may contain duplicate rows.
* Each row indicates the date, units, and `product_id` of each product sold.

Write a solution to find the average selling price for each product.  
`average_price` should be **rounded to 2 decimal places**. If a product has no sold units, its average selling price is assumed to be `0`.

Return the result table in **any order**.

---

## Example

### Input

`Prices` table:

| product_id | start_date | end_date | price |
| :--- | :--- | :--- | :--- |
| 1 | 2019-02-17 | 2019-02-28 | 5 |
| 1 | 2019-03-01 | 2019-03-22 | 20 |
| 2 | 2019-02-01 | 2019-02-20 | 15 |
| 2 | 2019-02-21 | 2019-03-31 | 30 |

`UnitsSold` table:

| product_id | purchase_date | units |
| :--- | :--- | :--- |
| 1 | 2019-02-25 | 100 |
| 1 | 2019-03-01 | 15 |
| 2 | 2019-02-10 | 200 |
| 2 | 2019-03-22 | 30 |

### Output

| product_id | average_price |
| :--- | :--- |
| 1 | 6.96 |
| 2 | 16.96 |

### Explanation

* **Average Selling Price** = Total Revenue / Total Units Sold
* **Product 1:** $((100 \times 5) + (15 \times 20)) / (100 + 15) = (500 + 300) / 115 = 800 / 115 = 6.96$
* **Product 2:** $((200 \times 15) + (30 \times 30)) / (200 + 30) = (3000 + 900) / 230 = 3900 / 230 = 16.96$

---

## Solution

```sql
SELECT p.product_id, IFNULL(ROUND(SUM(p.price * u.units) / SUM(u.units), 2), 0) AS average_price 
FROM Prices p 
LEFT JOIN UnitsSold u ON p.product_id = u.product_id
AND u.purchase_date BETWEEN p.start_date AND p.end_date
GROUP BY p.product_id;
```
---

# 1075. Project Employees I

**Difficulty:** Easy  
**Topic:** Joins & Aggregation

---

## Problem Statement

Table: `Project`

| Column Name | Type |
| :--- | :--- |
| `project_id` | int |
| `employee_id` | int |

* `(project_id, employee_id)` is the primary key of this table.
* `employee_id` is a foreign key referencing the `Employee` table.
* Each row indicates that an employee is assigned to a specific project.

Table: `Employee`

| Column Name | Type |
| :--- | :--- |
| `employee_id` | int |
| `name` | varchar |
| `experience_years` | int |

* `employee_id` is the primary key of this table.
* `experience_years` is guaranteed to be **NOT NULL**.

Write an SQL query that reports the **average experience years** of all employees for each project, **rounded to 2 decimal places**.

Return the result table in **any order**.

---

## Example

### Input

`Project` table:

| project_id | employee_id |
| :--- | :--- |
| 1 | 1 |
| 1 | 2 |
| 1 | 3 |
| 2 | 1 |
| 2 | 4 |

`Employee` table:

| employee_id | name | experience_years |
| :--- | :--- | :--- |
| 1 | Khaled | 3 |
| 2 | Ali | 2 |
| 3 | John | 1 |
| 4 | Doe | 2 |

### Output

| project_id | average_years |
| :--- | :--- |
| 1 | 2.00 |
| 2 | 2.50 |

### Explanation

* **Project 1:** Employees assigned are `1`, `2`, and `3`. Average experience = $(3 + 2 + 1) / 3 = 2.00$
* **Project 2:** Employees assigned are `1` and `4`. Average experience = $(3 + 2) / 2 = 2.50$

---

## Solution

```sql
SELECT p.project_id, ROUND(AVG(e.experience_years), 2) AS average_years 
FROM Project p
LEFT JOIN Employee e ON p.employee_id = e.employee_id
GROUP BY p.project_id;
```
---
# 1633. Percentage of Users Attended a Contest

**Difficulty:** Easy  
**Topic:** Aggregation, Subqueries & Sorting

---

## Problem Statement

Table: `Users`

| Column Name | Type |
| :--- | :--- |
| `user_id` | int |
| `user_name` | varchar |

* `user_id` is the primary key (column with unique values) for this table.
* Each row contains the name and ID of a user.

Table: `Register`

| Column Name | Type |
| :--- | :--- |
| `contest_id` | int |
| `user_id` | int |

* `(contest_id, user_id)` is the primary key (combination of columns with unique values) for this table.
* Each row contains the ID of a user and the contest they registered for.

Write a solution to find the percentage of users registered in each contest **rounded to two decimal places**.

Return the result table ordered by `percentage` in **descending order**. In case of a tie, order it by `contest_id` in **ascending order**.

---

## Example

### Input

`Users` table:

| user_id | user_name |
| :--- | :--- |
| 6 | Alice |
| 2 | Bob |
| 7 | Alex |

`Register` table:

| contest_id | user_id |
| :--- | :--- |
| 215 | 6 |
| 209 | 2 |
| 208 | 2 |
| 210 | 6 |
| 208 | 6 |
| 209 | 7 |
| 209 | 6 |
| 215 | 7 |
| 208 | 7 |
| 210 | 2 |
| 207 | 2 |
| 210 | 7 |

### Output

| contest_id | percentage |
| :--- | :--- |
| 208 | 100.0 |
| 209 | 100.0 |
| 210 | 100.0 |
| 215 | 66.67 |
| 207 | 33.33 |

### Explanation

Total users in system = 3.
* **Contests 208, 209, 210:** All 3 users registered $\rightarrow (3 / 3) \times 100 = 100.0\%$ (Tied percentages sorted by `contest_id` ascending).
* **Contest 215:** 2 users registered (Alice & Alex) $\rightarrow (2 / 3) \times 100 = 66.67\%$.
* **Contest 207:** 1 user registered (Bob) $\rightarrow (1 / 3) \times 100 = 33.33\%$.

---

## Solution

```sql
SELECT contest_id, ROUND((COUNT(user_id) / (SELECT COUNT(user_id) FROM Users) * 100), 2) AS percentage
FROM Register 
GROUP BY contest_id
ORDER BY percentage DESC, contest_id ASC;
```
---
# 1211. Queries Quality and Percentage

**Difficulty:** Easy  
**Topic:** Aggregation & Conditional Logic

---

## Problem Statement

Table: `Queries`

| Column Name | Type |
| :--- | :--- |
| `query_name` | varchar |
| `result` | varchar |
| `position` | int |
| `rating` | int |

* This table may contain duplicate rows.
* `position` has a value from `1` to `500`.
* `rating` has a value from `1` to `5`. A query with `rating < 3` is considered a **poor query**.

Definitions:
* **Quality**: The average of the ratio between query rating and its position (`rating / position`).
* **Poor Query Percentage**: The percentage of all queries for a given query name with a rating less than 3.

Write a solution to find each `query_name`, its `quality`, and its `poor_query_percentage`.  
Both metrics should be **rounded to 2 decimal places**.

Return the result table in **any order**.

---

## Example

### Input

`Queries` table:

| query_name | result | position | rating |
| :--- | :--- | :--- | :--- |
| Dog | Golden Retriever | 1 | 5 |
| Dog | German Shepherd | 2 | 5 |
| Dog | Mule | 200 | 1 |
| Cat | Shirazi | 5 | 2 |
| Cat | Siamese | 3 | 3 |
| Cat | Sphynx | 7 | 4 |

### Output

| query_name | quality | poor_query_percentage |
| :--- | :--- | :--- |
| Dog | 2.50 | 33.33 |
| Cat | 0.66 | 33.33 |

### Explanation

* **Dog Queries:**
  * Quality = $\frac{(5 / 1) + (5 / 2) + (1 / 200)}{3} = \frac{5 + 2.5 + 0.005}{3} = \frac{7.505}{3} = 2.50$
  * Poor Query Percentage = $\frac{1 \text{ (rating < 3)}}{3} \times 100 = 33.33\%$

* **Cat Queries:**
  * Quality = $\frac{(2 / 5) + (3 / 3) + (4 / 7)}{3} = \frac{0.4 + 1.0 + 0.5714}{3} = \frac{1.9714}{3} = 0.66$
  * Poor Query Percentage = $\frac{1 \text{ (rating < 3)}}{3} \times 100 = 33.33\%$

---

## Solution

```sql
SELECT query_name, ROUND(AVG(rating / position), 2) AS quality,
    ROUND(SUM(CASE WHEN rating < 3 THEN 1 ELSE 0 END) / COUNT(*) * 100, 2) AS poor_query_percentage
FROM Queries
WHERE query_name IS NOT NULL
GROUP BY query_name;
```
---

# 1193. Monthly Transactions I

**Difficulty:** Medium  
**Topic:** Aggregation & Conditional Grouping

---

## Problem Statement

Table: `Transactions`

| Column Name | Type |
| :--- | :--- |
| `id` | int |
| `country` | varchar |
| `state` | enum |
| `amount` | int |
| `trans_date` | date |

* `id` is the primary key of this table.
* `state` is an enum of type `["approved", "declined"]`.
* The table contains information about incoming transactions.

Write an SQL query to find for each month and country:
1. The total number of transactions (`trans_count`).
2. The number of approved transactions (`approved_count`).
3. Their total amount (`trans_total_amount`).
4. The total amount of approved transactions (`approved_total_amount`).

Return the result table in **any order**.

---

## Example

### Input

`Transactions` table:

| id | country | state | amount | trans_date |
| :--- | :--- | :--- | :--- | :--- |
| 121 | US | approved | 1000 | 2018-12-18 |
| 122 | US | declined | 2000 | 2018-12-19 |
| 123 | US | approved | 2000 | 2019-01-01 |
| 124 | DE | approved | 2000 | 2019-01-07 |

### Output

| month | country | trans_count | approved_count | trans_total_amount | approved_total_amount |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 2018-12 | US | 2 | 1 | 3000 | 1000 |
| 2019-01 | US | 1 | 1 | 2000 | 2000 |
| 2019-01 | DE | 1 | 1 | 2000 | 2000 |

---

## Solution

```sql
SELECT 
    DATE_FORMAT(trans_date, '%Y-%m') AS month,
    country,
    COUNT(*) AS trans_count,
    COUNT(CASE WHEN state = 'approved' THEN 1 ELSE NULL END) AS approved_count,
    SUM(amount) AS trans_total_amount,
    SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_total_amount
FROM Transactions
GROUP BY DATE_FORMAT(trans_date, '%Y-%m'), country;
```
---
# 1174. Immediate Food Delivery II

**Difficulty:** Medium  
**Topic:** Subqueries, Aggregation & Date Comparison

---

## Problem Statement

Table: `Delivery`

| Column Name | Type |
| :--- | :--- |
| `delivery_id` | int |
| `customer_id` | int |
| `order_date` | date |
| `customer_pref_delivery_date` | date |

* `delivery_id` is the primary key (column with unique values) for this table.
* **Immediate order:** `order_date` equals `customer_pref_delivery_date`.
* **Scheduled order:** `order_date` is different from `customer_pref_delivery_date`.
* **First order:** The order with the earliest `order_date` for a customer. Every customer has exactly one first order.

Write a solution to find the percentage of immediate orders among the **first orders** of all customers, **rounded to 2 decimal places**.

---

## Example

### Input

`Delivery` table:

| delivery_id | customer_id | order_date | customer_pref_delivery_date |
| :--- | :--- | :--- | :--- |
| 1 | 1 | 2019-08-01 | 2019-08-02 |
| 2 | 2 | 2019-08-02 | 2019-08-02 |
| 3 | 1 | 2019-08-11 | 2019-08-12 |
| 4 | 3 | 2019-08-24 | 2019-08-24 |
| 5 | 3 | 2019-08-21 | 2019-08-22 |
| 6 | 2 | 2019-08-11 | 2019-08-13 |
| 7 | 4 | 2019-08-09 | 2019-08-09 |

### Output

| immediate_percentage |
| :--- |
| 50.00 |

### Explanation

* **Customer 1:** First order is delivery `1` (2019-08-01 vs 2019-08-02) -> **Scheduled**
* **Customer 2:** First order is delivery `2` (2019-08-02 vs 2019-08-02) -> **Immediate**
* **Customer 3:** First order is delivery `5` (2019-08-21 vs 2019-08-22) -> **Scheduled**
* **Customer 4:** First order is delivery `7` (2019-08-09 vs 2019-08-09) -> **Immediate**

2 out of 4 customers have immediate first orders, so $(2 / 4) \times 100 = 50.00\%$.

---

## Solution

```sql
SELECT 
    ROUND(
        (COUNT(CASE WHEN first_order = prefer_date THEN 1 END) / COUNT(*)) * 100, 
        2
    ) AS immediate_percentage 
FROM (
    SELECT 
        customer_id, 
        MIN(order_date) AS first_order, 
        MIN(customer_pref_delivery_date) AS prefer_date 
    FROM delivery
    GROUP BY customer_id
) AS tb;
```
---
# 550. Game Play Analysis IV

**Difficulty:** Medium  
**Topic:** Subqueries, Joins & Date Functions

---

## Problem Statement

Table: `Activity`

| Column Name | Type |
| :--- | :--- |
| `player_id` | int |
| `device_id` | int |
| `event_date` | date |
| `games_played` | int |

* `(player_id, event_date)` is the primary key (combination of columns with unique values) of this table.
* Each row records a player logging in and playing games on a given date using a device.

Write a solution to report the **fraction** of players that logged in again on the day immediately following their first login date, **rounded to 2 decimal places**. 

In other words, count the number of players who logged in on `first_login_date + 1 day` and divide it by the total number of distinct players.

---

## Example

### Input

`Activity` table:

| player_id | device_id | event_date | games_played |
| :--- | :--- | :--- | :--- |
| 1 | 2 | 2016-03-01 | 5 |
| 1 | 2 | 2016-03-02 | 6 |
| 2 | 3 | 2017-06-25 | 1 |
| 3 | 1 | 2016-03-02 | 0 |
| 3 | 4 | 2018-07-03 | 5 |

### Output

| fraction |
| :--- |
| 0.33 |

### Explanation

* **Player 1:** First login date is `2016-03-01`. Next day is `2016-03-02` (logged in -> **Yes**).
* **Player 2:** First login date is `2017-06-25`. Next day is `2017-06-26` (no record -> **No**).
* **Player 3:** First login date is `2016-03-02`. Next day is `2016-03-03` (next record is 2018-07-03 -> **No**).

Only Player 1 logged back in on the consecutive day.  
Total distinct players = 3.  
Fraction = $1 / 3 = 0.33$.

---

## Solution

```sql
SELECT 
    ROUND((COUNT(CASE WHEN a.event_date = tb.first_date THEN 1 END) / COUNT(DISTINCT a.player_id)), 2) AS fraction 
FROM activity AS a 
LEFT JOIN (
    SELECT player_id, DATE_ADD(MIN(event_date), INTERVAL 1 DAY) AS first_date 
    FROM activity
    GROUP BY player_id
) AS tb
ON a.player_id = tb.player_id;
```
---