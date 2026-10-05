---
title: "leetcode-数据库58"
date: 2026-09-15T10:27:53+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 进店却未进行过交易的顾客

表：Visits

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| visit_id    | int     |
| customer_id | int     |
+-------------+---------+
visit_id 是该表中具有唯一值的列。
该表包含有关光临过购物中心的顾客的信息。


表：Transactions

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| transaction_id | int     |
| visit_id       | int     |
| amount         | int     |
+----------------+---------+
transaction_id 是该表中具有唯一值的列。
此表包含 visit_id 期间进行的交易的信息。


有一些顾客可能光顾了购物中心但没有进行交易。请你编写一个解决方案，来查找这些顾客的 ID ，以及他们只光顾不交易的次数。

返回以 任何顺序 排序的结果表。


```
-- 查找只光顾、未进行任何交易的顾客 ID 及其次数
SELECT
    v.customer_id,
    COUNT(*) AS count_no_trans   -- 每个 customer_id 对应的“无交易访问”次数
FROM Visits AS v
LEFT JOIN Transactions AS t
    ON v.visit_id = t.visit_id
WHERE t.visit_id IS NULL          -- 只保留在 Transactions 中找不到匹配的访问
GROUP BY v.customer_id;
```

用 LEFT JOIN ... WHERE t.visit_id IS NULL（反连接）替代 NOT IN 子查询：在 PostgreSQL 中反连接通常更快、更现代，且能避免 NOT IN 遇到 NULL 时的陷阱。

用 COUNT(\*) 替代 COUNT(customer_id)：语义相同，但 COUNT(\*) 更直接、可读性更好。
