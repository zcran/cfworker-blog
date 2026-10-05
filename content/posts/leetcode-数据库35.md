---
title: "leetcode-数据库35"
date: 2026-09-15T10:27:51+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 产品销售分析 III

销售表 Sales：

+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |
+-------------+-------+
(sale_id, year) 是这张表的主键（具有唯一值的列的组合）。
这张表的每一行都表示：编号 product_id 的产品在某一年的销售额。
一个产品可能在同一年内有多个销售条目。
请注意，价格是按每单位计的。
编写解决方案，选出每个售出过的产品 第一年 销售的 产品 id、年份、数量 和 价格。

对每个 product_id，找到其在Sales表中首次出现的最早年份。
返回该产品在该年度的 所有 销售条目。

返回一张有这些列的表：product_id，first_year，quantity 和 price。

结果表中的条目可以按 任意顺序 排列。


```
-- 思路：
-- 1. 对每个 product_id，按 year 升序排名。
-- 2. 取排名第 1 的行，即该产品最早年份的所有销售条目。
-- 3. 使用 RANK() 而非 ROW_NUMBER()，确保同一年份的多条记录都能保留。
-- 4. 用 CTE 代替子查询，结构更清晰。

WITH ranked AS (
    SELECT
        product_id,
        year,
        quantity,
        price,
        RANK() OVER (PARTITION BY product_id ORDER BY year) AS rk
    FROM Sales
)
SELECT
    product_id,
    year AS first_year,
    quantity,
    price
FROM ranked
WHERE rk = 1;
```

SELECT * 会多读 sale_id、product_id 等无用列，这里只取 product_id, year, quantity, price，减少 I/O。

CTE 代替子查询。结构更清晰，PostgreSQL 中 CTE 可被优化器内联，性能与子查询基本一致，可读性更好。
