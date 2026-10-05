---
title: "leetcode-数据库70"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 计算特殊奖金

表: Employees

+-------------+---------+
| 列名        | 类型     |
+-------------+---------+
| employee_id | int     |
| name        | varchar |
| salary      | int     |
+-------------+---------+
employee_id 是这个表的主键(具有唯一值的列)。
此表的每一行给出了雇员id ，名字和薪水。


编写解决方案，计算每个雇员的奖金。如果一个雇员的 id 是 奇数 并且他的名字不是以 'M' 开头，那么他的奖金是他工资的 100% ，否则奖金为 0 。

返回的结果按照 employee_id 排序。


```
-- 计算每位员工的奖金：
--   奇数 employee_id 且 name 不以 'M' 开头 → 奖金 = salary
--   否则 → 奖金 = 0
SELECT
    employee_id,
    CASE
        WHEN employee_id % 2 = 1
             AND name NOT LIKE 'M%'   -- 不以 'M' 开头
        THEN salary
        ELSE 0
    END AS bonus
FROM Employees
ORDER BY employee_id;
```

用 name NOT LIKE 'M%' 替代 name NOT REGEXP '^M'：判断“是否以 M 开头”用 LIKE 'M%' 更简单、更快，无需正则引擎，性能更优。
