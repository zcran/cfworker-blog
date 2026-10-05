---
title: "leetcode-数据库49"
date: 2026-09-15T10:27:52+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 餐馆营业额变化增长

表: Customer

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| customer_id   | int     |
| name          | varchar |
| visited_on    | date    |
| amount        | int     |
+---------------+---------+
在 SQL 中，(customer_id, visited_on) 是该表的主键。
该表包含一家餐馆的顾客交易数据。
visited_on 表示 (customer_id) 的顾客在 visited_on 那天访问了餐馆。
amount 是一个顾客某一天的消费总额。


你是餐馆的老板，现在你想分析一下可能的营业额变化增长（每天至少有一位顾客）。

计算以 7 天（某日期 + 该日期前的 6 天）为一个时间段的顾客消费平均值。average_amount 要 保留两位小数。

结果按 visited_on 升序排序。


```
-- 计算以 7 天为窗口（当天 + 前 6 天）的累计消费金额与平均金额
-- 注意两个"绊子"：
--   1) 同一天可能有多笔交易，需先按 visited_on 聚合
--   2) 前 6 天不完整（不足 7 天）的窗口要排除
WITH daily AS (
    SELECT
        visited_on,
        SUM(amount) AS amount
    FROM Customer
    GROUP BY visited_on
),
windowed AS (
    SELECT
        visited_on,
        SUM(amount) OVER (
            ORDER BY visited_on
            ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
        ) AS sum_amount
    FROM daily
)
SELECT
    visited_on,
    sum_amount                    AS amount,
    ROUND(sum_amount / 7.0, 2)    AS average_amount
FROM windowed
WHERE visited_on >= (SELECT MIN(visited_on) + INTERVAL '6 days' FROM Customer)
ORDER BY visited_on;
```
