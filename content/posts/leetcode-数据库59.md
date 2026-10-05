---
title: "leetcode-数据库59"
date: 2026-09-15T10:27:53+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 银行账户概要 II

表: Users

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| account      | int     |
| name         | varchar |
+--------------+---------+
account 是该表的主键(具有唯一值的列)。
该表的每一行都包含银行中每个用户的帐号。
表中不会有两个用户具有相同的名称。


表: Transactions

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| trans_id      | int     |
| account       | int     |
| amount        | int     |
| transacted_on | date    |
+---------------+---------+
trans_id 是该表主键(具有唯一值的列)。
该表的每一行包含了所有账户的交易改变情况。
如果用户收到了钱, 那么金额是正的; 如果用户转了钱, 那么金额是负的。
所有账户的起始余额为 0。


编写解决方案,  报告余额高于 10000 的所有用户的名字和余额. 账户的余额等于包含该账户的所有交易的总和。

返回结果表单 无顺序要求 。


```
-- 报告余额高于 10000 的所有用户名和余额
-- 余额 = 该账户所有交易金额之和（起始为 0）
SELECT
    u.name,
    SUM(t.amount) AS balance        -- 汇总每个账户的所有交易金额
FROM Users AS u
LEFT JOIN Transactions AS t
    ON u.account = t.account
GROUP BY u.account, u.name          -- 按账户聚合，附带 name 以符合 PG 的 GROUP BY 规则
HAVING SUM(t.amount) > 10000;       -- 只保留余额超过 10000 的用户
```

SUM(t.amount) 直接用于 HAVING，避免在外层再包一层子查询，性能更好。
