---
title: "leetcode-数据库81"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 查找合法邮箱

表：Users

+-----------------+---------+
| Column Name     | Type    |
+-----------------+---------+
| user_id         | int     |
| email           | varchar |
+-----------------+---------+
(user_id) 是这张表的唯一主键。
每一行包含用户的唯一 ID 和邮箱地址。
编写一个解决方案来查找所有 合法邮箱地址。一个合法的邮箱地址符合下述条件：

· 只包含一个 @ 符号。
· 以 .com 结尾。
· @ 符号前面的部分只包含 字母数字 字符和 下划线。
· @ 符号后面与 .com 前面的部分 包含 只有字母 的域名。

返回结果表以 user_id 升序 排序。


```
-- PostgreSQL 解决方案
-- 合法邮箱需同时满足：
--   1) 仅含一个 @
--   2) 以 .com 结尾
--   3) @ 前：字母、数字、下划线（至少一个）
--   4) @ 后到 .com 前：仅字母（至少一个）
-- 用 POSIX 正则 ~ 进行严格匹配，结果按 user_id 升序

SELECT
    user_id,
    email
FROM Users
WHERE email ~ '^[A-Za-z0-9_]+@[A-Za-z]+\.com$'
ORDER BY user_id ASC;
```
