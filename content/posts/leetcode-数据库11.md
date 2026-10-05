---
title: "leetcode-数据库11"
date: 2026-09-15T10:27:50+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 删除重复的电子邮箱

表: Person

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| email       | varchar |
+-------------+---------+
id 是该表的主键列(具有唯一值的列)。
该表的每一行包含一封电子邮件。电子邮件将不包含大写字母。


编写解决方案 删除 所有重复的电子邮件，只保留一个具有最小 id 的唯一电子邮件。

（对于 SQL 用户，请注意你应该编写一个 DELETE 语句而不是 SELECT 语句。）

（对于 Pandas 用户，请注意你应该直接修改 Person 表。）

运行脚本后，显示的答案是 Person 表。驱动程序将首先编译并运行您的代码片段，然后再显示 Person 表。Person 表的最终顺序 无关紧要 。

PostgreSQL
```
WITH ranked AS (
    SELECT id,
           ROW_NUMBER() OVER (
               PARTITION BY email
               ORDER BY id
           ) AS rn
    FROM Person
)
DELETE FROM Person
WHERE id IN (
    SELECT id
    FROM ranked
    WHERE rn > 1
);
```

一次分区排序：复杂度 O(N log N)，而自连接版本是 O(N²)（每行都要和所有同邮箱行比较）。

ROW_NUMBER() 而非 RANK()/DENSE_RANK()：这里要的是"每个邮箱只保留恰好一行（最小 id）"，即使有并列也只需保留一个，所以用 ROW_NUMBER() 最合适——它保证每个分区内编号唯一，rn = 1 恰好一行。
