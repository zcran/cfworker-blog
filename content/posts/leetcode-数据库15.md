---
title: "leetcode-数据库15"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 游戏玩法分析 IV


Table: Activity

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
（player_id，event_date）是此表的主键（具有唯一值的列的组合）。
这张表显示了某些游戏的玩家的活动情况。
每一行是一个玩家的记录，他在某一天使用某个设备注销之前登录并玩了很多游戏（可能是 0）。
编写解决方案，报告在首次登录的第二天再次登录的玩家的 比率，四舍五入到小数点后两位。换句话说，你需要计算从首次登录后的第二天登录的玩家数量，并将其除以总玩家数。

PostgreSQL
```
SELECT
    ROUND(
        COUNT(a.player_id)::numeric
        / COUNT(DISTINCT p.player_id),
        2
    ) AS fraction
FROM (
    SELECT player_id, MIN(event_date) AS login
    FROM Activity
    GROUP BY player_id
) p
LEFT JOIN Activity a
  ON a.player_id = p.player_id
    AND a.event_date = p.login + INTERVAL '1 day';
```

PostgreSQL
```
SELECT
    ROUND(
        AVG((a.event_date IS NOT NULL)::int),
        2
    ) AS fraction
FROM (
    SELECT player_id, MIN(event_date) AS login
    FROM Activity
    GROUP BY player_id
) p
LEFT JOIN Activity a
  ON a.player_id = p.player_id
    AND a.event_date = p.login + INTERVAL '1 day';
```
