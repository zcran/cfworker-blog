---
title: "leetcode-数据库93"
date: 2026-09-15T10:27:55+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 查找超预订员工

表：employees

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| employee_id   | int     |
| employee_name | varchar |
| department    | varchar |
+---------------+---------+
employee_id 是这张表的唯一主键。
每一行包含一个员工和他们部门的信息。
表：meetings

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| meeting_id    | int     |
| employee_id   | int     |
| meeting_date  | date    |
| meeting_type  | varchar |
| duration_hours| decimal |
+---------------+---------+
meeting_id 是这张表的唯一主键。
每一行表示一位员工参加的会议。meeting_type 可以是 'Team'，'Client' 或 'Training'。
编写一个解决方案来查找会议密集型的员工 -  在任何给定周内，花费超过 50% 工作时间在会议上的员工。

假定一个标准工作周是 40 小时
计算每位员工 每周（周一至周日）的 总会议小时数
员工如果每周会议时间超过 20 小时（40 小时工作时间的 50%），则被视为会议密集型。
统计每位员工有多少周是会议密集周
仅查找 至少 2 周会议密集的员工

返回结果表按会议密集周的数量降序排列，然后按员工姓名升序排列。


```
-- PostgreSQL 解决方案
-- 目标：找出“会议密集型”员工
--   按周（周一至周日）统计每位员工的总会议时长
--   单周会议时长 > 20 小时（40 小时的 50%）即为一个会议密集周
--   仅保留会议密集周 >= 2 的员工
-- 结果按会议密集周数降序、员工姓名升序

WITH weekly_meetings AS (
    -- 按员工 + 周（周一为起始）汇总会议时长
    SELECT
        m.employee_id,
        -- date_trunc('week', ...) 返回该周的周一，作为该周的唯一标识
        date_trunc('week', m.meeting_date)::date AS week_start,
        SUM(m.duration_hours) AS weekly_hours
    FROM meetings m
    GROUP BY m.employee_id, week_start
),
heavy_weeks AS (
    -- 筛选会议密集周：单周 > 20 小时
    SELECT
        employee_id,
        week_start
    FROM weekly_meetings
    WHERE weekly_hours > 20
)
SELECT
    e.employee_id,
    e.employee_name,
    e.department,
    COUNT(*) AS meeting_heavy_weeks   -- 该员工的会议密集周数量
FROM heavy_weeks hw
JOIN employees e ON e.employee_id = hw.employee_id
GROUP BY
    e.employee_id,
    e.employee_name,
    e.department
HAVING COUNT(*) >= 2                  -- 至少 2 周会议密集
ORDER BY
    meeting_heavy_weeks DESC,
    e.employee_name     ASC;
```
