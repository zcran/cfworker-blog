---
title: "leetcode-数据库71"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 2020年最后一次登录

表: Logins

+----------------+----------+
| 列名           | 类型      |
+----------------+----------+
| user_id        | int      |
| time_stamp     | datetime |
+----------------+----------+
(user_id, time_stamp) 是这个表的主键(具有唯一值的列的组合)。
每一行包含的信息是user_id 这个用户的登录时间。


编写解决方案以获取在 2020 年登录过的所有用户的本年度 最后一次 登录时间。结果集 不 包含 2020 年没有登录过的用户。

返回的结果集可以按 任意顺序 排列。


```
-- 获取在 2020 年登录过的所有用户在 2020 年的最后一次登录时间
SELECT
    user_id,
    MAX(time_stamp) AS last_stamp
FROM Logins
WHERE time_stamp >= '2020-01-01'
  AND time_stamp <  '2021-01-01'   -- 等价于限定 2020 年
GROUP BY user_id;
```
