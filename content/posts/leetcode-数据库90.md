---
title: "leetcode-数据库90"
date: 2026-09-15T10:27:55+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 寻找持续进步的员工

表：employees

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| employee_id | int     |
| name        | varchar |
+-------------+---------+
employee_id 是这张表的唯一主键。
每一行包含一名员工的信息。
表：performance_reviews

+-------------+------+
| Column Name | Type |
+-------------+------+
| review_id   | int  |
| employee_id | int  |
| review_date | date |
| rating      | int  |
+-------------+------+
review_id 是这张表的唯一主键。
每一行表示一名员工的绩效评估。评分在 1-5 的范围内，5分代表优秀，1分代表较差。
编写一个解决方案，以找到在过去三次评估中持续提高绩效的员工。

员工 至少需要 3 次评估 才能被考虑
员工过去的 3 次评估，评分必须 严格递增（每次评价都比上一次好）
根据 review_date 为每位员工分析最近的 3 次评估
进步分数 为最后 3 次评估中最后一次评分与最早一次评分之间的差值

返回结果表以 进步分数 降序 排序，然后以 名字 升序 排序。


```
-- PostgreSQL 解决方案
-- 目标：找出“最近三次评估评分严格递增”的员工
--   1) 每名员工至少 3 次评估
--   2) 按 review_date 取最近 3 次
--   3) 三次评分严格递增（最新 > 中间 > 最早）
--   进步分数 = 最新评分 - 最早评分
-- 结果按 improvement_score 降序、name 升序

WITH ranked AS (
    -- 按员工分组，按评估日期倒序编号：rn = 1 为最近一次
    SELECT
        pr.employee_id,
        pr.review_date,
        pr.rating,
        e.name,
        ROW_NUMBER() OVER (
            PARTITION BY pr.employee_id
            ORDER BY pr.review_date DESC
        ) AS rn
    FROM performance_reviews pr
    JOIN employees e ON e.employee_id = pr.employee_id
)
SELECT
    r1.employee_id,
    r1.name,
    r1.rating - r3.rating AS improvement_score   -- 最新 - 最早
FROM ranked r1
-- r1: 最近一次 (rn = 1)
-- r2: 中间一次 (rn = 2)
-- r3: 最早一次 (rn = 3)
JOIN ranked r2 ON r2.employee_id = r1.employee_id AND r2.rn = 2
JOIN ranked r3 ON r3.employee_id = r1.employee_id AND r3.rn = 3
WHERE r1.rn = 1
  AND r1.rating > r2.rating      -- 最新 > 中间
  AND r2.rating > r3.rating      -- 中间 > 最早（合起来即严格递增）
ORDER BY
    improvement_score DESC,
    r1.name           ASC;
```
