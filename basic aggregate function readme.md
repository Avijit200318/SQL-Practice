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