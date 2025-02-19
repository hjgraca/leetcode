# [550. Game Play Analysis IV](https://leetcode.com/problems/game-play-analysis-iv)

[中文文档](/solution/0500-0599/0550.Game%20Play%20Analysis%20IV/README.md)

## Description

<p>Table: <code>Activity</code></p>

<pre>
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
(player_id, event_date) is the primary key (combination of columns with unique values) of this table.
This table shows the activity of players of some games.
Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on someday using some device.
</pre>

<p>&nbsp;</p>

<p>Write a&nbsp;solution&nbsp;to report the <strong>fraction</strong> of players that logged in again on the day after the day they first logged in, <strong>rounded to 2 decimal places</strong>. In other words, you need to count the number of players that logged in for at least two consecutive days starting from their first login date, then divide that number by the total number of players.</p>

<p>The&nbsp;result format is in the following example.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input:</strong> 
Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-03-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+
<strong>Output:</strong> 
+-----------+
| fraction  |
+-----------+
| 0.33      |
+-----------+
<strong>Explanation:</strong> 
Only the player with id 1 logged back in after the first day he had logged in so the answer is 1/3 = 0.33
</pre>

## Solutions

<!-- tabs:start -->

### **SQL**

```sql
# Write your MySQL query statement below
SELECT ROUND(AVG(b.event_date IS NOT NULL), 2) AS fraction
FROM
    (
        SELECT
            player_id,
            MIN(event_date) AS event_date
        FROM activity
        GROUP BY player_id
    ) AS a
    LEFT JOIN activity AS b
        ON a.player_id = b.player_id AND DATEDIFF(a.event_date, b.event_date) = -1;
```

```sql
# Write your MySQL query statement below
WITH
    T AS (
        SELECT
            player_id,
            DATEDIFF(
                LEAD(event_date) OVER (
                    PARTITION BY player_id
                    ORDER BY event_date
                ),
                event_date
            ) AS diff,
            ROW_NUMBER() OVER (
                PARTITION BY player_id
                ORDER BY event_date
            ) AS rk
        FROM Activity
    )
SELECT
    ROUND(
        COUNT(DISTINCT IF(diff = 1, player_id, NULL)) / COUNT(
            DISTINCT player_id
        ),
        2
    ) AS fraction
FROM T
WHERE rk = 1;
```

<!-- tabs:end -->
