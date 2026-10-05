---
title: "leetcode-数据库16"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 至少有5名直接下属的经理

表: Employee

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| department  | varchar |
| managerId   | int     |
+-------------+---------+
id 是此表的主键（具有唯一值的列）。
该表的每一行表示雇员的名字、他们的部门和他们的经理的id。
如果managerId为空，则该员工没有经理。
没有员工会成为自己的管理者。


编写一个解决方案，找出至少有五个直接下属的经理。

以 任意顺序 返回结果表。

PostgreSQL
```
SELECT m.name
FROM Employee m
JOIN Employee r ON m.id = r.managerId
GROUP BY m.id, m.name
HAVING COUNT(r.id) >= 5;
```
