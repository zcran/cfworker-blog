---
title: "leetcode-数据库80"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 查找进步的学生

表：Scores

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| student_id  | int     |
| subject     | varchar |
| score       | int     |
| exam_date   | varchar |
+-------------+---------+
(student_id, subject, exam_date) 是这张表的主键。
每一行包含有关学生在特定考试日期特定科目成绩的信息。分数范围从 0 到 100（包括边界）。
编写一个解决方案来查找 进步的学生。如果 同时 满足以下两个条件，则该学生被认为是进步的：

在 同一科目 至少参加过两个不同日期的考试。
他们在该学科 最近的分数 比他们 第一次该学科考试的分数更高。
返回结果表以 student_id，subject 升序 排序。


```
-- PostgreSQL 解决方案
-- 对每个 (student_id, subject) 分组，用两次 ROW_NUMBER 标记最早与最近一次考试
-- 自连接取出首考与末考成绩，筛选“末考 > 首考”的进步学生
-- 只参加过一次考试的学生会被 rn_first = rn_last = 1 自动剔除

WITH ranked AS (
    SELECT
        student_id,
        subject,
        score,
        -- 按考试日期升序：rn_first = 1 即首次考试
        ROW_NUMBER() OVER (
            PARTITION BY student_id, subject
            ORDER BY exam_date ASC
        ) AS rn_first,
        -- 按考试日期降序：rn_last = 1 即最近一次考试
        ROW_NUMBER() OVER (
            PARTITION BY student_id, subject
            ORDER BY exam_date DESC
        ) AS rn_last
    FROM Scores
)
SELECT
    f.student_id,
    f.subject,
    f.score AS first_score,
    l.score AS latest_score
FROM ranked f
JOIN ranked l
    ON  f.student_id = l.student_id
    AND f.subject    = l.subject
WHERE
    f.rn_first = 1              -- 该组的第一次考试
    AND l.rn_last  = 1          -- 该组的最近一次考试
    AND f.rn_first <> f.rn_last -- 过滤只考过一次的学生（此时首末为同一行）
    AND f.score < l.score       -- 最近成绩严格高于首次成绩
ORDER BY
    f.student_id ASC,
    f.subject    ASC;
```
