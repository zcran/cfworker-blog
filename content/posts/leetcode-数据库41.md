---
title: "leetcode-数据库41"
date: 2026-09-15T10:27:52+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 指定日期的产品价格

产品数据表: Products

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| product_id    | int     |
| new_price     | int     |
| change_date   | date    |
+---------------+---------+
(product_id, change_date) 是此表的主键（具有唯一值的列组合）。
这张表的每一行分别记录了 某产品 在某个日期 更改后 的新价格。
一开始，所有产品价格都为 10。

编写一个解决方案，找出在 2019-08-16 所有产品的价格。

以 任意顺序 返回结果表。


```
-- 找出在 2019-08-16 时所有产品的价格
-- 规则：
--   1) 若某产品在 2019-08-16（含）之前有改价记录，取最近一次改价
--   2) 若某产品在 2019-08-16 之前从未改价（即首次改价晚于该日期），价格为默认的 10
WITH latest_price AS (
    SELECT DISTINCT ON (product_id)
        product_id,
        new_price
    FROM Products
    WHERE change_date <= DATE '2019-08-16'
    ORDER BY product_id, change_date DESC
)
SELECT
    p.product_id,
    COALESCE(lp.new_price, 10) AS price
FROM (SELECT DISTINCT product_id FROM Products) AS p
LEFT JOIN latest_price AS lp
    ON p.product_id = lp.product_id
ORDER BY p.product_id;
```

用 DISTINCT ON 取代 IN (子查询 + MAX)：PostgreSQL 特有的 DISTINCT ON (product_id) ... ORDER BY product_id, change_date DESC 一次扫描即可为每个产品取到最近一次改价，比"分组求 MAX + 元组 IN 回表"更高效、更易读。

用 LEFT JOIN + COALESCE 取代 UNION：原方案两个分支都要扫表，且在边界情况（产品只有一条且日期正好等于 2019-08-16）下 UNION 两支可能同时命中的隐患；改写为"取所有产品 → 左连最近改价 → 缺失则填 10"，语义更清晰、只扫一次主表。

DATE '...' 标准字面量：PostgreSQL 推荐的日期写法，避免隐式类型转换，利于索引使用。

COALESCE(..., 10) 处理默认价：直接把"从未改价"和"改价记录为空"两种情况统一兜底为 10，逻辑集中，无需再写 HAVING MIN(change_date) > ...。

CTE 提升可读性：latest_price 明确表达"每个产品在截止日前的最新价"，主查询只做连接与兜底。
