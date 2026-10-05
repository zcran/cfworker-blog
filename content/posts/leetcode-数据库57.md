---
title: "leetcode-数据库57"
date: 2026-09-15T10:27:53+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 患某种疾病的患者

患者信息表： Patients

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| patient_id   | int     |
| patient_name | varchar |
| conditions   | varchar |
+--------------+---------+
在 SQL 中，patient_id （患者 ID）是该表的主键。
'conditions' （疾病）包含 0 个或以上的疾病代码，以空格分隔。
这个表包含医院中患者的信息。


查询患有 I 类糖尿病的患者 ID （patient_id）、患者姓名（patient_name）以及其患有的所有疾病代码（conditions）。I 类糖尿病的代码总是包含前缀 DIAB1 。

按 任意顺序 返回结果表。


```
-- 查询患有 I 类糖尿病的患者信息
-- I 类糖尿病代码前缀为 DIAB1，可能出现在 conditions 开头或以空格分隔的任意位置
SELECT patient_id, patient_name, conditions
FROM Patients
WHERE conditions ~ '(^|\s)DIAB1';
```
