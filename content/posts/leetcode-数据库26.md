---
title: "leetcode-数据库26"
date: 2026-09-15T10:27:51+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 树节点

表：Tree

+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| p_id        | int  |
+-------------+------+
id 是该表中具有唯一值的列。
该表的每行包含树中节点的 id 及其父节点的 id 信息。
给定的结构总是一个有效的树。


树中的每个节点可以是以下三种类型之一：

"Leaf"：节点是叶子节点。
"Root"：节点是树的根节点。
"Inner"：节点既不是叶子节点也不是根节点。
编写一个解决方案来报告树中每个节点的类型。

以 任意顺序 返回结果表。


```
-- 思路：
-- 树中节点分三类：
--   Root : p_id IS NULL
--   Leaf : 有父节点，且自己不是任何节点的父节点（没有子节点）
--   Inner: 有父节点，且自己也是某些节点的父节点（有子节点）
--
-- 用一条 SELECT + CASE 完成分类，避免三次扫描 + UNION 去重排序。
-- 先算出“有子节点的 id 集合”，再对每行判断，更现代、更快、更可读。

WITH parent_ids AS (
    -- 所有作为父节点出现过的 id（即有子节点的节点）
    SELECT DISTINCT p_id AS id
    FROM Tree
    WHERE p_id IS NOT NULL
)
SELECT
    t.id,
    CASE
        WHEN t.p_id IS NULL THEN 'Root'
        WHEN t.id IN (SELECT id FROM parent_ids) THEN 'Inner'
        ELSE 'Leaf'
    END AS type
FROM Tree AS t
ORDER BY t.id;
```
