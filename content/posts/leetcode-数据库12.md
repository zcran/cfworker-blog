---
title: "leetcode-数据库12"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 上升的温度

表： Weather

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| recordDate    | date    |
| temperature   | int     |
+---------------+---------+
id 是该表具有唯一值的列。
没有具有相同 recordDate 的不同行。
该表包含特定日期的温度信息


编写解决方案，找出与之前（昨天的）日期相比温度更高的所有日期的 id 。

返回结果 无顺序要求 。


PostgreSQL
```
SELECT id
FROM (
    SELECT id,
           temperature,
           recordDate,
           LAG(temperature)  OVER (ORDER BY recordDate) AS prev_temp,
           LAG(recordDate)   OVER (ORDER BY recordDate) AS prev_date
    FROM Weather
) t
WHERE prev_temp IS NOT NULL
  AND temperature > prev_temp
  AND recordDate = prev_date + INTERVAL '1 day';
```

若 Weather(recordDate) 有索引，自连接可以走索引嵌套循环，性能很好；LAG 版本语义更清晰、扩展性更好（改 LAG(x, 2) 即可比前天）。两者性能接近，LAG 更现代。
