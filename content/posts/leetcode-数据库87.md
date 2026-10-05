---
title: "leetcode-数据库87"
date: 2026-09-15T10:27:55+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 查找类别推荐对

表：ProductPurchases

+-------------+------+
| Column Name | Type |
+-------------+------+
| user_id     | int  |
| product_id  | int  |
| quantity    | int  |
+-------------+------+
(user_id, product_id) 是这张表的唯一主键。
每一行代表用户以特定数量购买的一种产品。
表：ProductInfo

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_id  | int     |
| category    | varchar |
| price       | decimal |
+-------------+---------+
product_id 是这张表的唯一主键。
每一行表示一件商品的类别和价格。
亚马逊想要了解不同产品类别的购物模式。编写一个解决方案：

查找所有 类别对（其中 category1 < category2）
对于 每个类别对，确定 同时 购买了两类别产品的 不同用户 数量
如果至少有 3 个不同的客户购买了两个类别的产品，则类别对被视为 可报告的。

返回可报告类别对的结果表以 customer_count 降序 排序，并且为了防止排序持平，以 category1 字典序 升序 排序，然后以 category2 升序 排序。


```
-- PostgreSQL 解决方案
-- 目标：查找可报告的类别对 (category1 < category2)
--   1) 每个用户去重后与其购买过的每个类别配对
--   2) 自连接同一用户的两个不同类别
--   3) 统计同时购买两个类别的不同用户数
--   4) 仅保留 customer_count >= 3 的类别对
-- 结果按 customer_count 降序、category1 升序、category2 升序

WITH user_category AS (
    -- 先按用户+类别去重：同一用户在同一类别下买多件产品只算一次
    SELECT DISTINCT
        p.user_id,
        i.category
    FROM ProductPurchases p
    JOIN ProductInfo i ON p.product_id = i.product_id
)
SELECT
    uc1.category AS category1,
    uc2.category AS category2,
    COUNT(DISTINCT uc1.user_id) AS customer_count
FROM user_category uc1
-- 自连接：同一用户购买的两个不同类别，强制 category1 < category2 去重配对
JOIN user_category uc2
    ON  uc1.user_id  = uc2.user_id
    AND uc1.category < uc2.category
GROUP BY
    uc1.category,
    uc2.category
-- 至少 3 位不同客户同时购买两个类别才可报告
HAVING
    COUNT(DISTINCT uc1.user_id) >= 3
ORDER BY
    customer_count DESC,
    category1      ASC,
    category2      ASC;
```
