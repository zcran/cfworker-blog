---
title: "leetcode-数据库23"
date: 2026-09-15T10:27:51+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 体育馆的人流量

表：Stadium
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| visit_date    | date    |
| people        | int     |
+---------------+---------+
visit_date 是该表中具有唯一值的列。
每日人流量信息被记录在这三列信息中：序号 (id)、日期 (visit_date)、 人流量 (people)
每天只有一行记录，日期随着 id 的增加而增加


编写解决方案找出每行的人数大于或等于 100 且 id 连续的三行或更多行记录。

返回按 visit_date 升序排列 的结果表。

PostgreSQL
```
WITH filtered AS (
    SELECT
        id,
        visit_date,
        people,
        id - ROW_NUMBER() OVER (ORDER BY id) AS grp
    FROM Stadium
    WHERE people >= 100
),
grouped AS (
    SELECT
        id,
        visit_date,
        people,
        COUNT(*) OVER (PARTITION BY grp) AS cnt
    FROM filtered
)
SELECT id, visit_date, people
FROM grouped
WHERE cnt >= 3
ORDER BY visit_date;
```
只扫描一次 Stadium；
grp 用 id - ROW_NUMBER() 经典连续分组；
COUNT(*) OVER (PARTITION BY grp) 直接算出每组行数；
不需要 JOIN t2，减少一次连接和聚合；
