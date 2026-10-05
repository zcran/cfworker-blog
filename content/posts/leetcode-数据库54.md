---
title: "leetcode-数据库54"
date: 2026-09-15T10:27:53+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 排名靠前的旅行者

表：Users

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| name          | varchar |
+---------------+---------+
id 是该表中具有唯一值的列。
name 是用户名字。


表：Rides

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| user_id       | int     |
| distance      | int     |
+---------------+---------+
id 是该表中具有唯一值的列。
user_id 是本次行程的用户的 id, 而该用户此次行程距离为 distance 。


编写解决方案，报告每个用户的旅行距离。

返回的结果表单，以 travelled_distance 降序排列 ，如果有两个或者更多的用户旅行了相同的距离, 那么再以 name 升序排列 。


```
-- 报告每位用户的旅行总距离
-- 无行程的用户距离记为 0；按 travelled_distance 降序、name 升序排列
SELECT
    u.name,
    COALESCE(SUM(r.distance), 0) AS travelled_distance
FROM Users AS u
LEFT JOIN Rides AS r
    ON u.id = r.user_id
GROUP BY
    u.id,
    u.name
ORDER BY
    travelled_distance DESC,
    u.name ASC;
```
