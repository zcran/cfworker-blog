---
title: "leetcode-数据库72"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 按分类统计薪水

表: Accounts

+-------------+------+
| 列名        | 类型  |
+-------------+------+
| account_id  | int  |
| income      | int  |
+-------------+------+
在 SQL 中，account_id 是这个表的主键。
每一行都包含一个银行帐户的月收入的信息。


查询每个工资类别的银行账户数量。 工资类别如下：

"Low Salary"：所有工资 严格低于 20000 美元。
"Average Salary"： 包含 范围内的所有工资 [$20000, $50000] 。
"High Salary"：所有工资 严格大于 50000 美元。

结果表 必须 包含所有三个类别。 如果某个类别中没有帐户，则报告 0 。

按 任意顺序 返回结果表。


```
-- 统计每个工资类别的银行账户数量，必须包含所有三个类别（无账户则计 0）
-- Low Salary:     income < 20000
-- Average Salary: 20000 <= income <= 50000
-- High Salary:    income > 50000
SELECT
    'Low Salary' AS category,
    COUNT(*) FILTER (WHERE income < 20000) AS accounts_count
FROM Accounts
UNION ALL
SELECT
    'Average Salary' AS category,
    COUNT(*) FILTER (WHERE income BETWEEN 20000 AND 50000) AS accounts_count
FROM Accounts
UNION ALL
SELECT
    'High Salary' AS category,
    COUNT(*) FILTER (WHERE income > 50000) AS accounts_count
FROM Accounts;
```
