---
title: "leetcode-数据库14"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 游戏玩法分析 I

活动表 Activity：

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
在 SQL 中，表的主键是 (player_id, event_date)。
这张表展示了一些游戏玩家在游戏平台上的行为活动。
每行数据记录了一名玩家在退出平台之前，当天使用同一台设备登录平台后打开的游戏的数目（可能是 0 个）。


查询每位玩家 第一次登录平台的日期。

PostgreSQL
```
SELECT DISTINCT ON (player_id)
    player_id,
    event_date AS first_login
FROM Activity
ORDER BY player_id, event_date;
```

PostgreSQL
```
SELECT
    player_id,
    MIN(event_date) AS first_login
FROM Activity
GROUP BY player_id;
```
取聚合链
