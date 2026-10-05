---
title: "leetcode-数据库5"
date: 2026-09-15T10:27:49+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 连续出现的数字

表：Logs

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| num         | varchar |
+-------------+---------+
在 SQL 中，id 是该表的主键。
id 是一个自增列。


找出所有至少连续出现三次的数字。

返回的结果表中的数据可以按 任意顺序 排列。

MySQL
```
WITH diff AS (
    SELECT num,
           ROW_NUMBER() OVER (ORDER BY id)
         - ROW_NUMBER() OVER (PARTITION BY num ORDER BY id) AS diff_v
    FROM Logs
)
SELECT DISTINCT num AS ConsecutiveNums
FROM diff
GROUP BY num, diff_v
HAVING COUNT(*) >= 3;
```
不依赖 id 物理连续，只看 id 的排序顺序，即使 id 有断层也能正确识别连续段（因为用的是 ORDER BY id 的相对顺序）。

MySQL
```
SELECT DISTINCT
    l1.Num AS ConsecutiveNums
FROM
    Logs l1,
    Logs l2,
    Logs l3
WHERE
    l1.Id = l2.Id - 1
    AND l2.Id = l3.Id - 1
    AND l1.Num = l2.Num
    AND l2.Num = l3.Num
;

```
需要假设 id 严格连续无缺口
不依赖窗口函数，老版本数据库也能跑


PostgreSQL
```
SELECT DISTINCT num AS "ConsecutiveNums"
FROM (
    SELECT num,
           LAG(num, 1) OVER (ORDER BY id) AS prev1,
           LAG(num, 2) OVER (ORDER BY id) AS prev2
    FROM Logs
) t
WHERE num = prev1
  AND num = prev2;
```
只有一次窗口扫描，三个 LAG 在同一遍排序里算出（LAG(num,1) 和 LAG(num,2) 共享同一个窗口）。
没有 GROUP BY + HAVING 的聚合开销。
