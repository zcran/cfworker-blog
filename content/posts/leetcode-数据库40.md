---
title: "leetcode-数据库40"
date: 2026-09-15T10:27:52+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 市场分析 I

表： Users

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| user_id        | int     |
| join_date      | date    |
| favorite_brand | varchar |
+----------------+---------+
user_id 是此表主键（具有唯一值的列）。
表中描述了购物网站的用户信息，用户可以在此网站上进行商品买卖。


表： Orders

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| order_id      | int     |
| order_date    | date    |
| item_id       | int     |
| buyer_id      | int     |
| seller_id     | int     |
+---------------+---------+
order_id 是此表主键（具有唯一值的列）。
item_id 是 Items 表的外键（reference 列）。
（buyer_id，seller_id）是 User 表的外键。


表：Items

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| item_id       | int     |
| item_brand    | varchar |
+---------------+---------+
item_id 是此表的主键（具有唯一值的列）。


编写解决方案找出每个用户的注册日期和在 2019 年作为买家的订单总数。

以 任意顺序 返回结果表。


```
-- 统计每个用户在 2019 年作为买家的订单总数（无订单则记为 0）
-- 思路：先聚合出 2019 年每个买家的订单数，再左连接到 Users，保留所有用户
SELECT
    u.user_id                        AS buyer_id,
    u.join_date,
    COALESCE(o.cnt, 0)               AS orders_in_2019
FROM Users AS u
LEFT JOIN (
    SELECT
        buyer_id,
        COUNT(*) AS cnt
    FROM Orders
    WHERE order_date >= DATE '2019-01-01'
      AND order_date <= DATE '2019-12-31'
    GROUP BY buyer_id
) AS o
    ON u.user_id = o.buyer_id
ORDER BY u.user_id;
```

子查询保留为派生表而非 CTE：对于这种单次使用的简单聚合，派生表比 CTE 更轻量（PostgreSQL 12+ 中 CTE 默认可能被内联，但派生表语义更直接）。
