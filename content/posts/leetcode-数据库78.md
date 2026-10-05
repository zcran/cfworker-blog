---
title: "leetcode-数据库78"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 奇数和偶数交易

表：transactions

+------------------+------+
| Column Name      | Type |
+------------------+------+
| transaction_id   | int  |
| amount           | int  |
| transaction_date | date |
+------------------+------+
transactions_id 列唯一标识了表中的每一行。
这张表的每一行包含交易 id，金额总和和交易日期。
编写一个解决方案来查找每天 奇数 交易金额和 偶数 交易金额的 总和。如果某天没有奇数或偶数交易，显示为 0。

返回结果表以 transaction_date 升序 排序。


```
-- PostgreSQL 解决方案
-- 按交易日期分组，分别计算奇数金额总和与偶数金额总和
-- 结果按 transaction_date 升序排列

SELECT
    transaction_date,
    -- 奇数金额求和：用 FILTER 精确筛选，无匹配时 SUM 返回 NULL，用 COALESCE 转为 0
    COALESCE(SUM(amount) FILTER (WHERE amount % 2 <> 0), 0) AS odd_sum,
    -- 偶数金额求和：同理处理
    COALESCE(SUM(amount) FILTER (WHERE amount % 2 = 0), 0) AS even_sum
FROM
    transactions
GROUP BY
    transaction_date
ORDER BY
    transaction_date ASC;
```
