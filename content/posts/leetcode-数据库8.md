---
title: "leetcode-数据库8"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 从不订购的客户

Customers 表：

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+
在 SQL 中，id 是该表的主键。
该表的每一行都表示客户的 ID 和名称。
Orders 表：

+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| customerId  | int  |
+-------------+------+
在 SQL 中，id 是该表的主键。
customerId 是 Customers 表中 ID 的外键( Pandas 中的连接键)。
该表的每一行都表示订单的 ID 和订购该订单的客户的 ID。


找出所有从不点任何东西的顾客。

以 任意顺序 返回结果表。

PostgreSQL
```
SELECT c.name AS "Customers"
FROM Customers c
WHERE NOT EXISTS (
    SELECT 1
    FROM Orders o
    WHERE o.customerId = c.id
);
```
NOT EXISTS 是反半连接（anti-semi-join）语义，一旦在 Orders 里找到匹配行就立即短路，不需要把整个 Orders 表连接进来。
如果 Orders(customerId) 上有索引，优化器可以走索引查找 + 短路，对每个客户只探测是否存在订单，非常高效。
不会像 LEFT JOIN 那样产生中间连接结果再去过滤。
