---
title: "leetcode-数据库33"
date: 2026-09-15T10:27:51+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 合作过至少三次的演员和导演

ActorDirector 表：

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| actor_id    | int     |
| director_id | int     |
| timestamp   | int     |
+-------------+---------+
timestamp 是这张表的主键(具有唯一值的列).


编写解决方案找出合作过至少三次的演员和导演的 id 对 (actor_id, director_id)


```
-- 思路：
-- 1. 按 (actor_id, director_id) 分组。
-- 2. 统计每组合作次数，>= 3 即保留。
-- 3. timestamp 是主键，非空，用 COUNT(*) 即可，无需 COUNT(timestamp)。

SELECT actor_id, director_id
FROM ActorDirector
GROUP BY actor_id, director_id
HAVING COUNT(*) >= 3;
```
timestamp 是主键，非空，COUNT(timestamp) 与 COUNT(\*) 结果完全一致；
但 COUNT(\*) 语义更直接，PostgreSQL 优化器处理更自然。
