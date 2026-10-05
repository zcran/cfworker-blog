---
title: "leetcode-数据库64"
date: 2026-09-15T10:27:53+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 求关注者的数量

表： Followers

+-------------+------+
| Column Name | Type |
+-------------+------+
| user_id     | int  |
| follower_id | int  |
+-------------+------+
(user_id, follower_id) 是这个表的主键（具有唯一值的列的组合）。
该表包含一个关注关系中关注者和用户的编号，其中关注者关注用户。


编写解决方案，对于每一个用户，返回该用户的关注者数量。

按 user_id 的顺序返回结果表。


```
-- 统计每个用户的关注者数量，按 user_id 升序返回
SELECT
    user_id,
    COUNT(*) AS followers_count
FROM Followers
GROUP BY user_id
ORDER BY user_id;
```
