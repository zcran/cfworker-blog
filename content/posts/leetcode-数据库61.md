---
title: "leetcode-数据库61"
date: 2026-09-15T10:27:53+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 修复表中的名字

表： Users

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| user_id        | int     |
| name           | varchar |
+----------------+---------+
user_id 是该表的主键(具有唯一值的列)。
该表包含用户的 ID 和名字。名字仅由小写和大写字符组成。


编写解决方案，修复名字，使得只有第一个字符是大写的，其余都是小写的。

返回按 user_id 排序的结果表。


```
-- 修复名字：首字母大写，其余字母小写
-- 结果按 user_id 升序排序
SELECT
    user_id,
    UPPER(LEFT(name, 1)) || LOWER(SUBSTRING(name FROM 2)) AS name
FROM Users
ORDER BY user_id;
```
