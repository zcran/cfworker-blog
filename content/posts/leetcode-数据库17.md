---
title: "leetcode-数据库17"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 员工奖金

表：Employee

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| empId       | int     |
| name        | varchar |
| supervisor  | int     |
| salary      | int     |
+-------------+---------+
empId 是该表中具有唯一值的列。
该表的每一行都表示员工的 id 和姓名，以及他们经理的 id 和他们的工资。


表：Bonus

+-------------+------+
| Column Name | Type |
+-------------+------+
| empId       | int  |
| bonus       | int  |
+-------------+------+
empId 是该表具有唯一值的列。
empId 是 Employee 表中 empId 的外键(reference 列)。
该表的每一行都包含一个员工的 id 和他们各自的奖金。


编写一个解决方案来报告满足以下任一条件的每个员工的姓名和奖金金额：

奖金 少于 1000 的员工。
没有任何奖金的员工。
以 任意顺序 返回结果表。

PostgreSQL
```
SELECT e.name, b.bonus
FROM Employee e
LEFT JOIN Bonus b USING (empId)
WHERE b.bonus < 1000 OR b.bonus IS NULL;
```
USING (empId) 是 PostgreSQL 支持的现代写法，语义和 ON e.empId = b.empId 一样，但更简洁。
