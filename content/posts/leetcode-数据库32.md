---
title: "leetcode-数据库32"
date: 2026-09-15T10:27:51+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 买下所有产品的客户

Customer 表：

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| customer_id | int     |
| product_key | int     |
+-------------+---------+
该表可能包含重复的行。
customer_id 不为 NULL。
product_key 是 Product 表的外键(reference 列)。
Product 表：

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_key | int     |
+-------------+---------+
product_key 是这张表的主键（具有唯一值的列）。


编写解决方案，报告 Customer 表中购买了 Product 表中所有产品的客户的 id。

返回结果表 无顺序要求 。


```
-- 思路：
-- 1. 对 Customer 按 customer_id 分组。
-- 2. 统计每个客户购买的不同产品数：COUNT(DISTINCT product_key)。
-- 3. 与 Product 表中的产品总数比较，相等即表示购买了所有产品。
-- 注意：Customer 可能包含重复行，所以必须用 DISTINCT 去重 product_key。

SELECT customer_id
FROM Customer
GROUP BY customer_id
HAVING COUNT(DISTINCT product_key) = (SELECT COUNT(*) FROM Product);
```
