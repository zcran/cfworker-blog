---
title: "leetcode-数据库4"
date: 2026-09-15T10:27:49+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 分数排名

表: Scores

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| score       | decimal |
+-------------+---------+
id 是该表的主键（有不同值的列）。
该表的每一行都包含了一场比赛的分数。Score 是一个有两位小数点的浮点值。


编写一个解决方案来查询分数的排名。排名按以下规则计算:

分数应按从高到低排列。
如果两个分数相等，那么两个分数的排名应该相同。
在排名相同的分数后，排名数应该是下一个连续的整数。换句话说，排名之间不应该有空缺的数字。

按 score 降序返回结果表。

MySQL
```
SELECT
  S.score,
  DENSE_RANK() OVER (
    ORDER BY
      S.score DESC
  ) AS 'rank'
FROM
  Scores S;

```

注意： MySQL 在 8.0 版本（2018 年 4 月 19 日）之前不支持窗口函数。一般来说，窗口函数直到 SQL:2003 才被引入 SQL


PostgreSQL
```
SELECT
  score,
  DENSE_RANK() OVER (ORDER BY score DESC) AS "rank"
FROM Scores;
```
