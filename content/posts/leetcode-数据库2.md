---
title: "leetcode-数据库2"
date: 2026-09-15T10:27:49+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 第二高的薪水

Employee 表：
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
id 是这个表的主键。
表的每一行包含员工的工资信息。


查询并返回 Employee 表中第二高的 不同 薪水 。如果不存在第二高的薪水，查询应该返回 null(Pandas 则返回 None) 。


MySQL
```
SELECT
    (SELECT DISTINCT
            Salary
        FROM
            Employee
        ORDER BY Salary DESC
        LIMIT 1 OFFSET 1) AS SecondHighestSalary
;
```


PostgreSQL
```
SELECT (
    SELECT Salary
    FROM (
        SELECT Salary,
               DENSE_RANK() OVER (ORDER BY Salary DESC) AS rk
        FROM Employee
    ) t
    WHERE rk = 2
    LIMIT 1
) AS SecondHighestSalary;
```


为什么本题不首选它	简单场景下 LIMIT OFFSET 更短更快，窗口函数是"杀鸡用牛刀"
窗口函数真正的用武之地	组内 TopN、组内排名、环比/累计、去重取最新等分区排名场景


常见的窗口函数：

排名类：ROW_NUMBER()、RANK()、DENSE_RANK()、NTILE()
聚合类：SUM()、AVG()、COUNT()、MAX()、MIN()
偏移类：LAG()、LEAD()、FIRST_VALUE()、

三种排名函数：
假设薪水数据为：100, 100, 90, 80

Salary	ROW_NUMBER	RANK	DENSE_RANK
100	1	1	1
100	2	1	1
90	3	3	2
80	4	4	3

ROW_NUMBER()：不管是否重复，连续编号（1,2,3,4）
RANK()：重复值并列，跳号（1,1,3,4）
DENSE_RANK()：重复值并列，不跳号（1,1,2,3）← 本题要求"不同薪水"的第二高，所以用它
