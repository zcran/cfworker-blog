---
title: "leetcode-数据库68"
date: 2026-09-15T10:27:53+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 员工的直属部门

表：Employee

+---------------+---------+
| Column Name   |  Type   |
+---------------+---------+
| employee_id   | int     |
| department_id | int     |
| primary_flag  | varchar |
+---------------+---------+
这张表的主键为 employee_id, department_id (具有唯一值的列的组合)
employee_id 是员工的ID
department_id 是部门的ID，表示员工与该部门有关系
primary_flag 是一个枚举类型，值分别为('Y', 'N'). 如果值为'Y',表示该部门是员工的直属部门。 如果值是'N',则否


一个员工可以属于多个部门。当一个员工加入超过一个部门的时候，他需要决定哪个部门是他的直属部门。请注意，当员工只加入一个部门的时候，那这个部门将默认为他的直属部门，虽然表记录的值为'N'.

请编写解决方案，查出员工所属的直属部门。

返回结果 没有顺序要求 。


```
-- 找出每位员工的直属部门：
--   1) 员工只属于一个部门时，该部门默认是直属部门（即使 primary_flag = 'N'）
--   2) 员工属于多个部门时，primary_flag = 'Y' 的部门才是直属部门
WITH dept_count AS (
    SELECT
        employee_id,
        department_id,
        primary_flag,
        COUNT(*) OVER (PARTITION BY employee_id) AS dept_cnt  -- 该员工所属部门总数
    FROM Employee
)
SELECT
    employee_id,
    department_id
FROM dept_count
WHERE dept_cnt = 1
   OR primary_flag = 'Y';
```
