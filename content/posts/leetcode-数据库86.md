---
title: "leetcode-数据库86"
date: 2026-09-15T10:27:55+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 查找推荐产品对

表：ProductPurchases

+-------------+------+
| Column Name | Type |
+-------------+------+
| user_id     | int  |
| product_id  | int  |
| quantity    | int  |
+-------------+------+
(user_id, product_id) 是这张表的唯一主键。
每一行代表用户以特定数量购买的产品。
表：ProductInfo

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_id  | int     |
| category    | varchar |
| price       | decimal |
+-------------+---------+
product_id 是这张表的唯一主键。
每一行表示一个产品的类别和价格。
亚马逊希望根据 共同购买模式 实现 “购买此商品的用户还购买了...” 功能。编写一个解决方案以实现：

识别 被同一客户一起频繁购买的 不同 产品对（其中 product1_id < product2_id）
对于 每个产品对，确定有多少客户购买了这两种产品
如果 至少有 3 位不同的 客户同时购买了这两种产品，则认为该 产品对 适合推荐。

返回结果表以 customer_count  降序 排序，并且为了避免排序持平，以 product1_id 升序 排序，并以 product2_id 升序 排序。


```
-- PostgreSQL 解决方案
-- 目标：找出被同一客户一起购买的产品对 (product1_id < product2_id)
--       统计同时购买这两种产品的不同客户数
--       仅保留 customer_count >= 3 的产品对
-- 结果按 customer_count 降序、product1_id 升序、product2_id 升序

SELECT
    pp1.product_id    AS product1_id,
    pp2.product_id    AS product2_id,
    pi1.category      AS product1_category,
    pi2.category      AS product2_category,
    COUNT(DISTINCT pp1.user_id) AS customer_count
FROM ProductPurchases pp1
-- 自连接：同一用户购买的两个不同产品，强制 product1_id < product2_id 去重配对
JOIN ProductPurchases pp2
    ON  pp1.user_id    = pp2.user_id
    AND pp1.product_id < pp2.product_id
-- 分别关联产品信息，补全类别
JOIN ProductInfo pi1 ON pp1.product_id = pi1.product_id
JOIN ProductInfo pi2 ON pp2.product_id = pi2.product_id
GROUP BY
    pp1.product_id,
    pp2.product_id,
    pi1.category,
    pi2.category
-- 至少 3 位不同客户共同购买才推荐
HAVING
    COUNT(DISTINCT pp1.user_id) >= 3
ORDER BY
    customer_count DESC,
    product1_id    ASC,
    product2_id    ASC;
```
