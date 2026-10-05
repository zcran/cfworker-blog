---
title: "leetcode-数据库9"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 部门工资最高的员工

表： Employee

+--------------+---------+
| 列名          | 类型    |
+--------------+---------+
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |
+--------------+---------+
在 SQL 中，id是此表的主键。
departmentId 是 Department 表中 id 的外键（在 Pandas 中称为 join key）。
此表的每一行都表示员工的 id、姓名和工资。它还包含他们所在部门的 id。


表： Department

+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+
在 SQL 中，id 是此表的主键列。
此表的每一行都表示一个部门的 id 及其名称。


查找出每个部门中薪资最高的员工。
按 任意顺序 返回结果表。

PostgreSQL
```
SELECT
    d.name AS "Department",
    e.name AS "Employee",
    e.salary AS "Salary"
FROM (
    SELECT *,
           DENSE_RANK() OVER (PARTITION BY departmentId ORDER BY salary DESC) AS rk
    FROM Employee
) e
JOIN Department d ON e.departmentId = d.id
WHERE e.rk = 1;
```

一次扫描 + 分区排序：DENSE_RANK() OVER (PARTITION BY departmentId ORDER BY salary DESC) 在一次扫描中同时完成"按部门分组"和"按薪水降序排名"，不需要先聚合出每部门最高薪、再回表比较。

避免行值 IN 的二次匹配：GROUP BY 要先算出每部门最高薪，再拿 (departmentId, salary) 去匹配，相当于对 Employee 扫两遍 + 一次连接。窗口函数只扫一遍。

DENSE_RANK() 处理并列最高薪：如果同一部门有多人并列最高薪，DENSE_RANK() 都会给 rk = 1，全部保留，符合"每个部门薪资最高的员工（可能多个）"的语义。这点比 ROW_NUMBER() 更正确。
