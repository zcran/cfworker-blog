---
title: "leetcode-数据库29"
date: 2026-09-15T10:27:51+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 有趣的电影

表：cinema

+----------------+----------+
| Column Name    | Type     |
+----------------+----------+
| id             | int      |
| movie          | varchar  |
| description    | varchar  |
| rating         | float    |
+----------------+----------+
id 是该表的主键(具有唯一值的列)。
每行包含有关电影名称、类型和评级的信息。
评级为 [0,10] 范围内的小数点后 2 位浮点数。


编写解决方案，找出所有影片描述为 非 boring (不无聊) 的并且 id 为奇数 的影片。

返回结果按 rating 降序排列。


```
-- 思路：
-- 1. 筛选 id 为奇数的影片：在 PostgreSQL 里可用 id % 2 = 1，
--    或用更符合标准 SQL 的 MOD(id, 2) = 1。
-- 2. 筛选 description 不为 'boring' 的影片。
-- 3. 按 rating 降序排列。
-- 注意：若 description 可能为 NULL，用 <> 'boring' 会因 NULL 比较返回 UNKNOWN 而被过滤掉；
--       若想保留 NULL，可写成 description IS DISTINCT FROM 'boring'。

SELECT *
FROM Cinema
WHERE MOD(id, 2) = 1
  AND description <> 'boring'
ORDER BY rating DESC;
```
