---
title: "leetcode-数据库74"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 丢失信息的雇员

表: Employees

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| employee_id | int     |
| name        | varchar |
+-------------+---------+
employee_id 是该表中具有唯一值的列。
每一行表示雇员的 id 和他的姓名。
表: Salaries

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| employee_id | int     |
| salary      | int     |
+-------------+---------+
employee_id 是该表中具有唯一值的列。
每一行表示雇员的 id 和他的薪水。


编写解决方案，找到所有 丢失信息 的雇员 id。当满足下面一个条件时，就被认为是雇员的信息丢失：

雇员的 姓名 丢失了，或者
雇员的 薪水信息 丢失了
返回这些雇员的 id  employee_id ， 从小到大排序 。


```
-- 找出信息丢失的雇员：姓名或薪水任一缺失（即只在其中一张表中出现）
-- 思路：合并两张表的 employee_id，只出现一次的就是缺失一方信息的雇员
SELECT employee_id
FROM (
    SELECT employee_id FROM Employees
    UNION ALL
    SELECT employee_id FROM Salaries
) AS all_ids
GROUP BY employee_id
HAVING COUNT(*) = 1   -- 只出现一次 → 另一张表缺失
ORDER BY employee_id;
```
