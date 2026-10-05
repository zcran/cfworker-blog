---
title: "leetcode-数据库30"
date: 2026-09-15T10:27:51+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 换座位

表: Seat

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| student     | varchar |
+-------------+---------+
id 是该表的主键（唯一值）列。
该表的每一行都表示学生的姓名和 ID。
ID 序列始终从 1 开始并连续增加。


编写解决方案来交换每两个连续的学生的座位号。如果学生的数量是奇数，则最后一个学生的id不交换。

按 id 升序 返回结果表。


```
-- 思路：
-- 1. 偶数 id：与前一 id 交换，新 id = id - 1。
-- 2. 奇数 id 且不是最后一个：与后一 id 交换，新 id = id + 1。
-- 3. 奇数 id 且是最后一个（总数为奇数时）：保持不变，新 id = id。
-- 优化点：
--   - 用窗口函数 MAX(id) OVER () 一次算出最大 id，
--     避免每行都执行一次相关子查询 (SELECT MAX(id) FROM Seat)。
--   - 用 CTE 先算好 new_id 和最大 id，逻辑更清晰、可读性更好。
--   - 最终按 new_id 升序输出。

WITH t AS (
    SELECT
        id,
        student,
        MAX(id) OVER () AS max_id
    FROM Seat
)
SELECT
    CASE
        WHEN id % 2 = 0 THEN id - 1
        WHEN id % 2 = 1 AND id < max_id THEN id + 1
        ELSE id
    END AS id,
    student
FROM t
ORDER BY id;
```
