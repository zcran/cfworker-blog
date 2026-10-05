---
title: "leetcode-数据库37"
date: 2026-09-15T10:27:51+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 销售分析 III

表： Product

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
| unit_price   | int     |
+--------------+---------+
product_id 是该表的主键（具有唯一值的列）。
该表的每一行显示每个产品的名称和价格。
表：Sales

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| seller_id   | int     |
| product_id  | int     |
| buyer_id    | int     |
| sale_date   | date    |
| quantity    | int     |
| price       | int     |
+------ ------+---------+
这个表可能有重复的行。
product_id 是 Product 表的外键（reference 列）。
该表的每一行包含关于一个销售的一些信息。


编写解决方案，报告 2019年春季 才售出的产品。即 仅 在 2019-01-01 （含）至 2019-03-31 （含）之间出售的商品。

以 任意顺序 返回结果表。


```
-- 使用 INNER JOIN + GROUP BY 筛选出只在 2019 春季售出的产品
-- 逻辑：按产品分组，若该产品的所有销售记录都落在 [2019-01-01, 2019-03-31] 内，
--       则 MIN(sale_date) 和 MAX(sale_date) 必然也在此区间内。
SELECT
    p.product_id,
    p.product_name
FROM product AS p
INNER JOIN sales AS s
    ON p.product_id = s.product_id
GROUP BY
    p.product_id,
    p.product_name
HAVING
    MIN(s.sale_date) >= DATE '2019-01-01'
    AND MAX(s.sale_date) <= DATE '2019-03-31';
```

PostgreSQL 严格要求 SELECT 中非聚合列必须出现在 GROUP BY 中

MIN/MAX 保证所有销售日期都在区间内，比 NOT EXISTS 或 COUNT 更简洁高效（只需一次扫描 + 聚合）。

使用 DATE '...' 字面量：PostgreSQL 标准日期写法，避免隐式类型转换，比字符串比较更安全、更快。
