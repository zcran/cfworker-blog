---
title: "leetcode-数据库65"
date: 2026-09-15T10:27:53+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 每位经理的下属员工数量

表：Employees

+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| employee_id | int      |
| name        | varchar  |
| reports_to  | int      |
| age         | int      |
+-------------+----------+
employee_id 是这个表中具有不同值的列。
该表包含员工以及需要听取他们汇报的上级经理的 ID 的信息。 有些员工不需要向任何人汇报（reports_to 为空）。


对于此问题，我们将至少有一个其他员工需要向他汇报的员工，视为一个经理。

编写一个解决方案来返回需要听取汇报的所有经理的 ID、名称、直接向该经理汇报的员工人数，以及这些员工的平均年龄，其中该平均年龄需要四舍五入到最接近的整数。

返回的结果集需要按照 employee_id 进行排序。


```
-- 找出所有经理（至少有一名直接下属），统计其直接下属人数及平均年龄（四舍五入到整数）
SELECT
    e.employee_id,
    e.name,
    COUNT(r.employee_id)              AS reports_count,   -- 直接下属人数
    ROUND(AVG(r.age)::numeric, 0)     AS average_age      -- 平均年龄，四舍五入到整数
FROM Employees AS e
JOIN Employees AS r
    ON e.employee_id = r.reports_to
GROUP BY e.employee_id, e.name        -- PostgreSQL 要求非聚合列都出现在 GROUP BY 中
ORDER BY e.employee_id;
```
