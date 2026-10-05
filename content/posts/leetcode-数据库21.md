---
title: "leetcode-数据库21"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 大的国家

World 表：

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| name        | varchar |
| continent   | varchar |
| area        | int     |
| population  | int     |
| gdp         | bigint  |
+-------------+---------+
name 是该表的主键（具有唯一值的列）。
这张表的每一行提供：国家名称、所属大陆、面积、人口和 GDP 值。


如果一个国家满足下述两个条件之一，则认为该国是 大国 ：

面积至少为 300 万平方公里（即，3000000 km2），或者
人口至少为 2500 万（即 25000000）

编写解决方案找出 大国 的国家名称、人口和面积。

按 任意顺序 返回结果表。

PostgreSQL
```
SELECT name, population, area
FROM World
WHERE population >= 25000000
   OR area >= 3000000;
```
一次扫描
