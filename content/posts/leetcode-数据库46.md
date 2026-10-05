---
title: "leetcode-数据库46"
date: 2026-09-15T10:27:52+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 查询结果的质量和占比

Queries 表：

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| query_name  | varchar |
| result      | varchar |
| position    | int     |
| rating      | int     |
+-------------+---------+
此表可能有重复的行。
此表包含了一些从数据库中收集的查询信息。
“位置”（position）列的值为 1 到 500 。
“评分”（rating）列的值为 1 到 5 。评分小于 3 的查询被定义为质量很差的查询。


将查询结果的质量 quality 定义为：

各查询结果的评分与其位置之间比率的平均值。

将劣质查询百分比 poor_query_percentage 定义为：

评分小于 3 的查询结果占全部查询结果的百分比。

编写解决方案，找出每次的 query_name 、 quality 和 poor_query_percentage。

quality 和 poor_query_percentage 都应 四舍五入到小数点后两位 。

以 任意顺序 返回结果表。


```
-- 计算每个 query_name 的 quality 与 poor_query_percentage
-- quality = AVG(rating / position)
-- poor_query_percentage = 评分 < 3 的记录占比 × 100
-- 两者均四舍五入到两位小数
SELECT
    query_name,
    ROUND(AVG(rating::numeric / position), 2)                       AS quality,
    ROUND(
        COUNT(*) FILTER (WHERE rating < 3) * 100.0 / COUNT(*),
        2
    )                                                                AS poor_query_percentage
FROM Queries
WHERE query_name IS NOT NULL
GROUP BY query_name
ORDER BY query_name;
```
