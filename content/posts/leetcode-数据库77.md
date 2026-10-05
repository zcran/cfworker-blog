---
title: "leetcode-数据库77"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 各赛事的用户注册率

用户表： Users

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| user_id     | int     |
| user_name   | varchar |
+-------------+---------+
user_id 是该表的主键(具有唯一值的列)。
该表中的每行包括用户 ID 和用户名。


注册表： Register

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| contest_id  | int     |
| user_id     | int     |
+-------------+---------+
(contest_id, user_id) 是该表的主键(具有唯一值的列的组合)。
该表中的每行包含用户的 ID 和他们注册的赛事。


编写解决方案统计出各赛事的用户注册百分率，保留两位小数。

返回的结果表按 percentage 的 降序 排序，若相同则按 contest_id 的 升序 排序。


```
-- PostgreSQL 解决方案
-- 统计各赛事注册率，百分比保留两位小数
-- 按 percentage 降序，若相同按 contest_id 升序
SELECT
    r.contest_id,
    ROUND(COUNT(r.user_id) * 100.0 / (SELECT COUNT(*) FROM Users), 2) AS percentage
FROM
    Register r
GROUP BY
    r.contest_id
ORDER BY
    percentage DESC,
    r.contest_id ASC;
```
