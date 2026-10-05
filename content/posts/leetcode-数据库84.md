---
title: "leetcode-数据库84"
date: 2026-09-15T10:27:55+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 分析组织层级

表：Employees

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| employee_id    | int     |
| employee_name  | varchar |
| manager_id     | int     |
| salary         | int     |
| department     | varchar |
+----------------+----------+
employee_id 是这张表的唯一主键。
每一行包含关于一名员工的信息，包括他们的 ID，姓名，他们经理的 ID，薪水和部门。
顶级经理（CEO）的 manager_id 是空的。
编写一个解决方案来分析组织层级并回答下列问题：

1. 层级：对于每名员工，确定他们在组织中的层级（CEO 层级为 1，CEO 的直接下属员工层级为 2，以此类推）。
2. 团队大小：对于每个是经理的员工，计算他们手下的（直接或间接下属）总员工数。
3. 薪资预算：对于每个经理，计算他们控制的总薪资预算（所有手下员工的工资总和，包括间接下属，加上自己的工资）。

返回结果表以 层级 升序 排序，然后以预算 降序 排序，最后以 employee_name 升序 排序。


```
-- PostgreSQL 解决方案
-- 目标：对每位员工计算
--   层级 level     ：CEO = 1，其直接下属 = 2，以此类推
--   团队大小 team_size：手下所有直接/间接下属总数（非经理则为 0）
--   薪资预算 budget  ：手下所有下属（含间接）工资 + 自己工资
-- 结果按 level 升序、budget 降序、employee_name 升序

WITH RECURSIVE
-- 步骤 1：自顶向下递归，确定每位员工的层级
org AS (
    SELECT
        employee_id,
        manager_id,
        1 AS level
    FROM Employees
    WHERE manager_id IS NULL          -- CEO（顶级经理）

    UNION ALL

    SELECT
        e.employee_id,
        e.manager_id,
        o.level + 1
    FROM Employees e
    JOIN org o ON e.manager_id = o.employee_id
),
-- 步骤 2：自底向上递归，把每位下属的工资累加到所有祖先的预算中
--         每条记录表示 “某位下属的工资，应计入其某位祖先（含自己）的预算”
budget_tree AS (
    -- 初始：每位员工把自己算入自己的预算
    SELECT
        employee_id AS ancestor_id,
        employee_id AS descendant_id,
        salary
    FROM Employees

    UNION ALL

    -- 递归：下属的工资向上传递给其经理
    SELECT
        e.manager_id AS ancestor_id,
        bt.descendant_id,
        bt.salary
    FROM budget_tree bt
    JOIN Employees e ON e.employee_id = bt.ancestor_id
    WHERE e.manager_id IS NOT NULL
),
-- 步骤 3：按祖先聚合出预算和团队大小
agg AS (
    SELECT
        ancestor_id AS employee_id,
        SUM(salary)                        AS budget,
        COUNT(*) - 1                       AS team_size  -- 减去自己
    FROM budget_tree
    GROUP BY ancestor_id
)
SELECT
    e.employee_id,
    e.employee_name,
    o.level,
    a.team_size,
    a.budget
FROM Employees e
JOIN org  o ON o.employee_id = e.employee_id
JOIN agg  a ON a.employee_id = e.employee_id
ORDER BY
    o.level      ASC,
    a.budget     DESC,
    e.employee_name ASC;
```
