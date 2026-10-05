---
title: "leetcode-数据库27"
date: 2026-09-15T10:27:51+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 判断三角形

表: Triangle

+-------------+------+
| Column Name | Type |
+-------------+------+
| x           | int  |
| y           | int  |
| z           | int  |
+-------------+------+
在 SQL 中，(x, y, z)是该表的主键列。
该表的每一行包含三个线段的长度。


对每三个线段报告它们是否可以形成一个三角形。

以 任意顺序 返回结果表。


```
-- 思路：
-- 三角形判定：任意两边之和大于第三边。
-- 用 CASE 直接判断三组不等式，输出 'Yes' / 'No'。
-- 注意：PostgreSQL 中字符串别名不能像 MySQL 那样用单引号，
-- 列别名 'triangle' 要写成 AS triangle 或 AS "triangle"。

SELECT
    x,
    y,
    z,
    CASE
        WHEN x + y > z
         AND x + z > y
         AND y + z > x
        THEN 'Yes'
        ELSE 'No'
    END AS triangle
FROM Triangle;
```
