---
title: "leetcode-数据库53"
date: 2026-09-15T10:27:53+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 股票的资本损益

Stocks 表：

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| stock_name    | varchar |
| operation     | enum    |
| operation_day | int     |
| price         | int     |
+---------------+---------+
(stock_name, operation_day) 是这张表的主键(具有唯一值的列的组合)
operation 列使用的是一种枚举类型，包括：('Sell','Buy')
此表的每一行代表了名为 stock_name 的某支股票在 operation_day 这一天的操作价格。
此表可以保证，股票的每个“卖出”操作在前某一天都有相应的“买入”操作。并且，股票的每个“买入”操作在即将到来的某一天都有相应的“卖出”操作。


编写解决方案报告每只股票的 资本损益。

股票的 资本利得/损失 是指一次或多次买卖该股票后的总收益或损失。

以 任意顺序 返回结果表。


```
-- 计算每只股票的资本损益：卖出金额 - 买入金额
-- 思路：买入记为 -price，卖出记为 +price，按股票汇总求和
SELECT
    stock_name,
    SUM(
        CASE operation
            WHEN 'Buy'  THEN -price
            WHEN 'Sell' THEN  price
        END
    ) AS capital_gain_loss
FROM Stocks
GROUP BY stock_name
ORDER BY stock_name;
```
