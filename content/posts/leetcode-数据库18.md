---
title: "leetcode-数据库18"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 寻找用户推荐人

表: Customer

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| referee_id  | int     |
+-------------+---------+
在 SQL 中，id 是该表的主键列。
该表的每一行表示一个客户的 id、姓名以及推荐他们的客户的 id。
找出以下客户的姓名：

1. 被任何 id != 2 的用户推荐。
2. 没有被 任何用户推荐。

以 任意顺序 返回结果表。

PostgreSQL
```
SELECT name
FROM Customer
WHERE referee_id IS DISTINCT FROM 2;
```
