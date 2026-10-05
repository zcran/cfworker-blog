---
title: "leetcode-数据库43"
date: 2026-09-15T10:27:52+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 重新格式化部门表

表 Department：

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| revenue       | int     |
| month         | varchar |
+---------------+---------+
在 SQL 中，(id, month) 是表的联合主键。
这个表格有关于每个部门每月收入的信息。
月份（month）可以取下列值 ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"]。


重新格式化表格，使得 每个月 都有一个部门 id 列和一个收入列。

以 任意顺序 返回结果表。


```
-- 将按月存储的部门收入透视为宽表：每个部门一行，12 个月各一列
-- 使用 FILTER 子句替代 CASE WHEN，更贴近 PostgreSQL 的聚合语义，可读性更强
SELECT
    id,
    SUM(revenue) FILTER (WHERE month = 'Jan') AS Jan_Revenue,
    SUM(revenue) FILTER (WHERE month = 'Feb') AS Feb_Revenue,
    SUM(revenue) FILTER (WHERE month = 'Mar') AS Mar_Revenue,
    SUM(revenue) FILTER (WHERE month = 'Apr') AS Apr_Revenue,
    SUM(revenue) FILTER (WHERE month = 'May') AS May_Revenue,
    SUM(revenue) FILTER (WHERE month = 'Jun') AS Jun_Revenue,
    SUM(revenue) FILTER (WHERE month = 'Jul') AS Jul_Revenue,
    SUM(revenue) FILTER (WHERE month = 'Aug') AS Aug_Revenue,
    SUM(revenue) FILTER (WHERE month = 'Sep') AS Sep_Revenue,
    SUM(revenue) FILTER (WHERE month = 'Oct') AS Oct_Revenue,
    SUM(revenue) FILTER (WHERE month = 'Nov') AS Nov_Revenue,
    SUM(revenue) FILTER (WHERE month = 'Dec') AS Dec_Revenue
FROM Department
GROUP BY id
ORDER BY id;
```

SUM(...) FILTER (WHERE ...) 替代 SUM(CASE WHEN ...)：这是 SQL 标准、PostgreSQL 原生支持的聚合过滤语法，语义更直观（"对满足条件的行求和"），且执行计划与 CASE WHEN 等价甚至更优——优化器能更直接识别过滤条件。
