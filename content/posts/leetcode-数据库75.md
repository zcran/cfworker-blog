---
title: "leetcode-数据库75"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 上级经理已离职的公司员工

表: Employees

+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| employee_id | int      |
| name        | varchar  |
| manager_id  | int      |
| salary      | int      |
+-------------+----------+
在 SQL 中，employee_id 是这个表的主键。
这个表包含了员工，他们的薪水和上级经理的id。
有一些员工没有上级经理（其 manager_id 是空值）。


查找这些员工的id，他们的薪水严格少于$30000 并且他们的上级经理已离职。当一个经理离开公司时，他们的信息需要从员工表中删除掉，但是表中的员工的manager_id  这一列还是设置的离职经理的id 。

返回的结果按照employee_id 从小到大排序。


```
-- 找出薪水严格少于 30000 且上级经理已离职的员工
-- 经理离职 → 其 employee_id 已不在 Employees 表中
SELECT employee_id
FROM Employees AS e
WHERE e.salary < 30000
  AND e.manager_id IS NOT NULL                                    -- 排除无上级的情况
  AND NOT EXISTS (
      SELECT 1
      FROM Employees AS m
      WHERE m.employee_id = e.manager_id                          -- 该 manager_id 是否仍在职
  )
ORDER BY employee_id;
```
