---
title: "leetcode-数据库13"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 行程和用户

表：Trips
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| id          | int      |
| client_id   | int      |
| driver_id   | int      |
| city_id     | int      |
| status      | enum     |
| request_at  | varchar  |
+-------------+----------+
id 是这张表的主键（具有唯一值的列）。
这张表中存所有出租车的行程信息。每段行程有唯一 id ，其中 client_id 和 driver_id 是 Users 表中 users_id 的外键。
status 是一个表示行程状态的枚举类型，枚举成员为(‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’) 。

表：Users

+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| users_id    | int      |
| banned      | enum     |
| role        | enum     |
+-------------+----------+
users_id 是这张表的主键（具有唯一值的列）。
这张表中存所有用户，每个用户都有一个唯一的 users_id ，role 是一个表示用户身份的枚举类型，枚举成员为 (‘client’, ‘driver’, ‘partner’) 。
banned 是一个表示用户是否被禁止的枚举类型，枚举成员为 (‘Yes’, ‘No’) 。

取消率 的计算方式如下：(被司机或乘客取消的非禁止用户生成的订单数量) / (非禁止用户生成的订单总数)。

编写解决方案找出 "2013-10-01" 至 "2013-10-03" 期间有 至少 一次行程的非禁止用户（乘客和司机都必须未被禁止）的 取消率。非禁止用户即 banned 为 No 的用户，禁止用户即 banned 为 Yes 的用户。其中取消率 Cancellation Rate 需要四舍五入保留 两位小数 。

返回结果表中的数据 无顺序要求 。

PostgreSQL
```
SELECT
    t.request_at AS "Day",
    ROUND(
        COUNT(*) FILTER (WHERE t.status <> 'completed')::numeric
        / COUNT(*),
        2
    ) AS "Cancellation Rate"
FROM Trips t
JOIN Users c ON c.users_id = t.client_id AND c.banned = 'No'
JOIN Users d ON d.users_id = t.driver_id AND d.banned = 'No'
WHERE t.request_at BETWEEN '2013-10-01' AND '2013-10-03'
GROUP BY t.request_at;
```

用 INNER JOIN 只保留非禁止用户，比 NOT IN 子查询更高效（Users 表有主键索引，JOIN 走索引查找）。

PostgreSQL
```
SELECT
    t.request_at AS "Day",
    ROUND(
        COUNT(*) FILTER (WHERE t.status <> 'completed')::numeric
        / COUNT(*),
        2
    ) AS "Cancellation Rate"
FROM Trips t
WHERE NOT EXISTS (
        SELECT 1 FROM Users u
        WHERE u.users_id = t.client_id AND u.banned = 'Yes'
    )
    AND NOT EXISTS (
        SELECT 1 FROM Users u
        WHERE u.users_id = t.driver_id AND u.banned = 'Yes'
    )
    AND t.request_at BETWEEN '2013-10-01' AND '2013-10-03'
GROUP BY t.request_at;
```
