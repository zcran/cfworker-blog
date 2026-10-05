---
title: "leetcode-数据库76"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 每位教师所教授的科目种类的数量

表: Teacher

+-------------+------+
| Column Name | Type |
+-------------+------+
| teacher_id  | int  |
| subject_id  | int  |
| dept_id     | int  |
+-------------+------+
在 SQL 中，(subject_id, dept_id) 是该表的主键。
该表中的每一行都表示，该教师(teacher_id)在该系(dept_id)里教授的课程(subject_id)。


查询每位老师在大学里教授的科目种类的数量。

以 任意顺序 返回结果表。


```
-- 查询每位老师教授的科目种类数量（去重计数）
SELECT
    teacher_id,
    COUNT(DISTINCT subject_id) AS cnt
FROM Teacher
GROUP BY teacher_id;
```
