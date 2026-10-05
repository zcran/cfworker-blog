---
title: "leetcode-数据库24"
date: 2026-09-15T10:27:51+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 好友申请 II ：谁有最多的好友

RequestAccepted 表：

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| requester_id   | int     |
| accepter_id    | int     |
| accept_date    | date    |
+----------------+---------+
(requester_id, accepter_id) 是这张表的主键(具有唯一值的列的组合)。
这张表包含发送好友请求的人的 ID ，接收好友请求的人的 ID ，以及好友请求通过的日期。


编写解决方案，找出拥有最多的好友的人和他拥有的好友数目。

生成的测试用例保证拥有最多好友数目的只有 1 个人。

PostgreSQL
```
-- 思路：
-- 1. 好友关系是双向的：A 请求 B 通过后，A 和 B 互为好友。
-- 2. 把 (requester_id, accepter_id) 与反向的 (accepter_id, requester_id) 合并，
--    得到所有“用户 -> 好友”的边。
-- 3. 因为 (requester_id, accepter_id) 是主键，合并后的边天然不重复，
--    所以可以用 UNION ALL 代替 UNION，避免额外的去重排序/哈希开销。
-- 4. 按用户分组，统计好友数，取最大的一条。
-- 题目保证拥有最多好友的人唯一，因此 ORDER BY ... LIMIT 1 安全。

SELECT
    u.id,
    COUNT(*) AS num
FROM (
    -- 正向：请求者 -> 接受者
    SELECT requester_id AS id, accepter_id AS friend_id
    FROM RequestAccepted

    UNION ALL

    -- 反向：接受者 -> 请求者
    SELECT accepter_id AS id, requester_id AS friend_id
    FROM RequestAccepted
) AS u
GROUP BY u.id
ORDER BY num DESC
LIMIT 1;
```
