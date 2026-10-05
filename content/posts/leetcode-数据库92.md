---
title: "leetcode-数据库92"
date: 2026-09-15T10:27:55+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 寻找燃油效率提升的驾驶员

表：drivers

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| driver_id   | int     |
| driver_name | varchar |
+-------------+---------+
driver_id 是这张表的唯一主键。
每一行都包含一个司机的信息。
表：trips

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| trip_id       | int     |
| driver_id     | int     |
| trip_date     | date    |
| distance_km   | decimal |
| fuel_consumed | decimal |
+---------------+---------+
trip_id 是这张表的唯一主键。
每一行表示一名司机完成的一次行程，包括该次行程行驶的距离和消耗的燃油量。
编写一个解决方案，通过 比较 司机在 上半年 和 下半年 的 平均燃油效率 来找出 燃油效率有所提高 的司机。

通过 distance_km / fuel_consumed 计算 每次 行程的 燃油效率。
上半年：一月到六月，下半年：七月到十二月
只包含在上半年和下半年都有行程的司机
通过（second_half_avg - first_half_avg）计算 提升效率。
将所有结果 四舍五入 到小数点后 2 位

返回结果表按提升效率 降序 排列，然后按司机姓名 升序 排列。


```
-- PostgreSQL 解决方案
-- 目标：找出燃油效率提升的司机
--   单次行程燃油效率 = distance_km / fuel_consumed
--   上半年 = 1-6 月，下半年 = 7-12 月
--   仅保留上半年和下半年都有行程的司机
--   提升效率 = 下半年均值 - 上半年均值
-- 结果四舍五入到两位小数，按提升效率降序、司机姓名升序

WITH trip_efficiency AS (
    -- 计算每次行程的燃油效率，并标记上下半年
    SELECT
        driver_id,
        CASE
            WHEN EXTRACT(MONTH FROM trip_date) <= 6 THEN 'H1'
            ELSE 'H2'
        END AS half,
        distance_km / fuel_consumed AS efficiency   -- 逐次行程效率
    FROM trips
    WHERE fuel_consumed > 0    -- 防止除零
),
half_avg AS (
    -- 按司机与半年聚合，计算半年平均效率
    SELECT
        driver_id,
        AVG(efficiency) FILTER (WHERE half = 'H1') AS first_half_avg,
        AVG(efficiency) FILTER (WHERE half = 'H2') AS second_half_avg
    FROM trip_efficiency
    GROUP BY driver_id
)
SELECT
    d.driver_id,
    d.driver_name,
    ROUND(h.first_half_avg::numeric, 2)                                  AS first_half_avg,
    ROUND(h.second_half_avg::numeric, 2)                                 AS second_half_avg,
    ROUND((h.second_half_avg - h.first_half_avg)::numeric, 2)            AS efficiency_improvement
FROM half_avg h
JOIN drivers d ON d.driver_id = h.driver_id
WHERE
    h.first_half_avg  IS NOT NULL      -- 上半年必须有行程
    AND h.second_half_avg IS NOT NULL  -- 下半年必须有行程
    AND h.second_half_avg > h.first_half_avg   -- 效率提升
ORDER BY
    efficiency_improvement DESC,
    d.driver_name          ASC;
```
