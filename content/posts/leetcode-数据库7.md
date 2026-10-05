---
title: "leetcode-数据库7"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 查找重复的电子邮箱

表: Person

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| email       | varchar |
+-------------+---------+
id 是该表的主键（具有唯一值的列）。
此表的每一行都包含一封电子邮件。电子邮件不包含大写字母。


编写解决方案来报告所有重复的电子邮件。 请注意，可以保证电子邮件字段不为 NULL。

以 任意顺序 返回结果表。

PostgreSQL
```
SELECT email AS "Email"
FROM Person
GROUP BY email
HAVING COUNT(*) > 1;
```
GROUP BY email 利用 email 上的索引（如果有）做分组，避免全表排序。
HAVING COUNT(*) > 1 在分组后过滤，只保留重复项。
只扫描一遍表，没有子查询、没有自连接。
