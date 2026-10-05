---
title: "leetcode-数据库50"
date: 2026-09-15T10:27:52+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 列出指定时间段内所有的下单产品

表: Products

+------------------+---------+
| Column Name      | Type    |
+------------------+---------+
| product_id       | int     |
| product_name     | varchar |
| product_category | varchar |
+------------------+---------+
product_id 是该表主键(具有唯一值的列)。
该表包含该公司产品的数据。


表: Orders

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| product_id    | int     |
| order_date    | date    |
| unit          | int     |
+---------------+---------+
该表可能包含重复行。
product_id 是表单 Products 的外键（reference 列）。
unit 是在日期 order_date 内下单产品的数目。


写一个解决方案，要求获取在 2020 年 2 月份下单的数量不少于 100 的产品的名字和数目。

返回结果表单的 顺序无要求 。


```
-- 获取 2020 年 2 月下单数量不少于 100 的产品名称与总数量
SELECT
    p.product_name,
    SUM(o.unit) AS unit
FROM Products AS p
INNER JOIN Orders AS o
    ON p.product_id = o.product_id
WHERE o.order_date >= DATE '2020-02-01'
  AND o.order_date <  DATE '2020-03-01'
GROUP BY
    p.product_id,
    p.product_name
HAVING SUM(o.unit) >= 100
ORDER BY p.product_name;
```
