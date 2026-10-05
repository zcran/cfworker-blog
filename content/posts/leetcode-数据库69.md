---
title: "leetcode-数据库69"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 每个产品在不同商店的价格

表：Products

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_id  | int     |
| store1      | int     |
| store2      | int     |
| store3      | int     |
+-------------+---------+
在 SQL 中，这张表的主键是 product_id（产品Id）。
每行存储了这一产品在不同商店 store1, store2, store3 的价格。
如果这一产品在商店里没有出售，则值将为 null。


请你重构 Products 表，查询每个产品在不同商店的价格，使得输出的格式变为(product_id, store, price) 。如果这一产品在商店里没有出售，则不输出这一行。

输出结果表中的 顺序不作要求 。


```
-- 将 Products 表从宽表重构为长表：(product_id, store, price)
-- 每个产品在每个商店的价格作为一行，未出售（NULL）的商店不输出
SELECT product_id, 'store1' AS store, store1 AS price FROM Products WHERE store1 IS NOT NULL
UNION ALL
SELECT product_id, 'store2' AS store, store2 AS price FROM Products WHERE store2 IS NOT NULL
UNION ALL
SELECT product_id, 'store3' AS store, store3 AS price FROM Products WHERE store3 IS NOT NULL;
```

使用 UNION ALL 而非 UNION：因为不同商店的记录天然不会重复，UNION ALL 不做去重，性能更好，语义也更准确。

每个分支内先 WHERE storeX IS NOT NULL 过滤：既满足“未出售不输出”的要求，也减少了参与 UNION ALL 的行数。
