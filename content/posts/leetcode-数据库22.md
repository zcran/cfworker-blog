---
title: "leetcode-数据库22"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 超过 5 名学生的课

表: Courses

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| student     | varchar |
| class       | varchar |
+-------------+---------+
(student, class)是该表的主键（不同值的列的组合）。
该表的每一行表示学生的名字和他们注册的课程。


查询 至少有 5 个学生 的所有课程。

以 任意顺序 返回结果表。

PostgreSQL
```
SELECT class
FROM Courses
GROUP BY class
HAVING COUNT(*) >= 5;
```
避免使用COUNT(DISTINCT x)：需要在每个分组内对 x 去重，通常会触发排序或哈希；
