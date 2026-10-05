---
title: "leetcode-数据库6"
date: 2026-09-15T10:27:49+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 超过经理收入的员工

表：Employee

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| salary      | int     |
| managerId   | int     |
+-------------+---------+
id 是该表的主键（具有唯一值的列）。
该表的每一行都表示雇员的ID、姓名、工资和经理的ID。


编写解决方案，找出收入比经理高的员工。

以 任意顺序 返回结果表。

MySQL
```
select a.Name AS 'Employee'
from Employee as a,Employee as b
where a.managerId = b.id and a.salary > b.salary;
```

PostgreSQL
```
SELECT e.name AS "Employee"
FROM Employee e
WHERE EXISTS (
    SELECT 1
    FROM Employee m
    WHERE m.id = e.managerId
      AND e.salary > m.salary
);
```
EXISTS 是半连接（semi-join）语义，一旦找到匹配的经理行就停止扫描，不需要把两张表的所有匹配行都物化出来。
自连接会生成所有满足条件的 (a, b) 组合，如果多个员工共享同一个经理，会有重复展开。
当 Employee(id) 有主键索引、managerId 有索引时，EXISTS 可以走索引查找 + 短路，通常比自连接更省。
半连接不产生中间笛卡尔展开，managerId 有索引时几乎是最优计划。
显式 EXISTS 是现代 SQL 处理"存在性判断"的标准模式。
