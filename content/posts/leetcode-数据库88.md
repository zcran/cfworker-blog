---
title: "leetcode-数据库88"
date: 2026-09-15T10:27:55+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 季节性销售分析

表：sales

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| sale_id       | int     |
| product_id    | int     |
| sale_date     | date    |
| quantity      | int     |
| price         | decimal |
+---------------+---------+
sale_id 是这张表的唯一主键。
每一行包含一件产品的销售信息，包括 product_id，销售日期，销售数量，以及单价。
表：products

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| product_id    | int     |
| product_name  | varchar |
| category      | varchar |
+---------------+---------+
product_id 是这张表的唯一主键。
每一行包含一件产品的信息，包括它的名字和分类。
编写一个解决方案来找到每个季节最受欢迎的产品分类。季节定义如下：

冬季：十二月，一月，二月
春季：三月，四月，五月
夏季：六月，七月，八月
秋季：九月，十月，十一月
一个 分类 的 受欢迎度 由某个 季节 的 总销售量 决定。如果有并列，选择总收入最高的类别 (quantity × price)。如果依然并列，返回字典序更小的分类。

返回结果表以季节 升序 排序。



```
-- PostgreSQL 解决方案
-- 目标：为每个季节找出最受欢迎的产品类别
--   季节定义：Winter(12,1,2) / Spring(3,4,5) / Summer(6,7,8) / Fall(9,10,11)
--   受欢迎度 = 该季节总销量 SUM(quantity)
--   并列规则：总收入更高者优先；仍并列则分类字典序更小者优先
-- 结果按 season 升序

WITH season_stats AS (
    SELECT
        -- 根据月份归类季节
        CASE
            WHEN EXTRACT(MONTH FROM s.sale_date) IN (12, 1, 2) THEN 'Winter'
            WHEN EXTRACT(MONTH FROM s.sale_date) IN (3, 4, 5)  THEN 'Spring'
            WHEN EXTRACT(MONTH FROM s.sale_date) IN (6, 7, 8)  THEN 'Summer'
            ELSE 'Fall'  -- 9, 10, 11
        END AS season,
        p.category,
        SUM(s.quantity)                  AS total_quantity,   -- 受欢迎度
        SUM(s.quantity * s.price)        AS total_revenue     -- 用于并列决胜
    FROM sales s
    JOIN products p ON s.product_id = p.product_id
    GROUP BY season, p.category
),
ranked AS (
    -- 用窗口函数一次性完成排序：季节内按 销量↓、收入↓、分类↑
    SELECT
        season,
        category,
        total_quantity,
        total_revenue,
        ROW_NUMBER() OVER (
            PARTITION BY season
            ORDER BY total_quantity DESC, total_revenue DESC, category ASC
        ) AS rn
    FROM season_stats
)
SELECT
    season,
    category,
    total_quantity,
    total_revenue
FROM ranked
WHERE rn = 1                       -- 每个季节取排名第一的类别
ORDER BY season ASC;
```
