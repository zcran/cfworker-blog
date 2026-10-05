---
title: "leetcode-数据库44"
date: 2026-09-15T10:27:52+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 每月交易 I

表：Transactions

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| country       | varchar |
| state         | enum    |
| amount        | int     |
| trans_date    | date    |
+---------------+---------+
id 是这个表的主键。
该表包含有关传入事务的信息。
state 列类型为 ["approved", "declined"] 之一。


编写一个 sql 查询来查找每个月和每个国家/地区的事务数及其总金额、已批准的事务数及其总金额。

以 任意顺序 返回结果表。


```
-- 按月、按国家统计：事务总数/总金额，以及已批准事务数/总金额
-- 使用 TO_CHAR 格式化月份，使用 FILTER 做条件聚合，更贴近 PostgreSQL 原生语法
SELECT
    TO_CHAR(trans_date, 'YYYY-MM')                          AS month,
    country,
    COUNT(*)                                                AS trans_count,
    COUNT(*)     FILTER (WHERE state = 'approved')          AS approved_count,
    SUM(amount)                                             AS trans_total_amount,
    COALESCE(SUM(amount) FILTER (WHERE state = 'approved'), 0) AS approved_total_amount
FROM Transactions
GROUP BY
    TO_CHAR(trans_date, 'YYYY-MM'),
    country
ORDER BY
    month,
    country;
```
