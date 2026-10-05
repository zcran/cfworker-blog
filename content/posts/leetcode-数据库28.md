---
title: "leetcode-数据库28"
date: 2026-09-15T10:27:51+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 只出现一次的最大数字

MyNumbers 表：

+-------------+------+
| Column Name | Type |
+-------------+------+
| num         | int  |
+-------------+------+
该表可能包含重复项（换句话说，在SQL中，该表没有主键）。
这张表的每一行都含有一个整数。


单一数字 是在 MyNumbers 表中只出现一次的数字。

找出最大的 单一数字 。如果不存在 单一数字 ，则返回 null 。


```
-- 思路：
-- 1. 按 num 分组，找出只出现一次的数字（单一数字）。
-- 2. 用 MAX 取其中最大值；如果没有单一数字，MAX 自动返回 NULL。

SELECT MAX(num) AS num
FROM (
    SELECT num
    FROM MyNumbers
    GROUP BY num
    HAVING COUNT(*) = 1
) AS t;
```
