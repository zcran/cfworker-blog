---
title: "leetcode-数据库91"
date: 2026-09-15T10:27:55+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 寻找 COVID 康复患者

表：patients

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| patient_id  | int     |
| patient_name| varchar |
| age         | int     |
+-------------+---------+
patient_id 是这张表的唯一主键。
每一行表示一个患者的信息。
表：covid_tests

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| test_id     | int     |
| patient_id  | int     |
| test_date   | date    |
| result      | varchar |
+-------------+---------+
test_id 是这张表的唯一主键。
每一行代表一个 COVID 检测结果。结果可以是阳性、阴性或不确定。
编写一个解决方案以找到从 COVID 中康复的患者——那些曾经检测呈阳性但后来检测呈阴性的患者。

患者如果 至少有一次阳性 检测结果后，在 之后的日期 至少有一次 阴性 检测结果，则被认为已康复。
计算从 首次阳性检测 结果到 该阳性检测 后的 首次阴性检测结果 之间的 康复时间（以天为单位）
仅包括 同时具有阳性及阴性检测结果的患者

返回结果表以 recovery_time 升序 排序，然后以 patient_name 升序 排序。


```
-- PostgreSQL 解决方案
-- 目标：找出从 COVID 康复的患者
--   康复定义：至少一次阳性后，在之后的日期至少一次阴性
--   康复时间 = 首次阳性日期 到 该阳性之后首次阴性日期 的天数
-- 仅包含同时具有阳性与阴性结果的患者
-- 结果按 recovery_time 升序、patient_name 升序

WITH first_positive AS (
    -- 每位患者的首次阳性检测日期
    SELECT
        patient_id,
        MIN(test_date) AS first_pos_date
    FROM covid_tests
    WHERE result = 'Positive'
    GROUP BY patient_id
),
first_negative_after AS (
    -- 首次阳性之后，每位患者的首次阴性检测日期
    SELECT
        fp.patient_id,
        MIN(ct.test_date) AS first_neg_date
    FROM first_positive fp
    JOIN covid_tests ct
        ON  ct.patient_id = fp.patient_id
        AND ct.result     = 'Negative'
        AND ct.test_date  > fp.first_pos_date   -- 必须是阳性之后
    GROUP BY fp.patient_id
)
SELECT
    p.patient_id,
    p.patient_name,
    p.age,
    -- 康复天数 = 首次阴性日期 - 首次阳性日期
    (fn.first_neg_date - fp.first_pos_date) AS recovery_time
FROM first_positive fp
-- 只保留有“阳性后阴性”记录的患者
JOIN first_negative_after fn ON fn.patient_id = fp.patient_id
JOIN patients p              ON p.patient_id  = fp.patient_id
ORDER BY
    recovery_time ASC,
    p.patient_name ASC;
```
