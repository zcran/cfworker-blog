---
title: "leetcode-数据库38"
date: 2026-09-15T10:27:52+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 查询近30天活跃用户数

表：Activity

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| session_id    | int     |
| activity_date | date    |
| activity_type | enum    |
+---------------+---------+
该表可能包含重复数据。
activity_type 列是 ENUM(category) 类型， 从 ('open_session'， 'end_session'， 'scroll_down'， 'send_message') 取值。
该表记录社交媒体网站的用户活动。
注意，每个会话只属于一个用户。


编写解决方案，统计截至 2019-07-27（包含2019-07-27），近 30 天的每日活跃用户数（当天只要有一条活动记录，即为活跃用户）。

以 任意顺序 返回结果表。

注意：（'open_session'，'end_session'，'scroll_down'，'send_message'）中的任何活动将被视为用户在某一天活跃的有效活动。




```
-- 统计截至 2019-07-27（含）近 30 天的每日活跃用户数
-- 近 30 天窗口：activity_date 在 [2019-06-28, 2019-07-27] 之间
-- 说明：2019-07-27 往前推 29 天 = 2019-06-28，共 30 天
SELECT
    activity_date              AS day,
    COUNT(DISTINCT user_id)    AS active_users
FROM Activity
WHERE activity_date >= DATE '2019-06-28'
  AND activity_date <= DATE '2019-07-27'
GROUP BY activity_date
ORDER BY day;
```
