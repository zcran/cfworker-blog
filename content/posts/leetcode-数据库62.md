---
title: "leetcode-数据库62"
date: 2026-09-15T10:27:53+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 无效的推文

表：Tweets

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| tweet_id       | int     |
| content        | varchar |
+----------------+---------+
在 SQL 中，tweet_id 是这个表的主键。
content 只包含字母数字字符，'!'，' '，不包含其它特殊字符。
这个表包含某社交媒体 App 中所有的推文。


查询所有无效推文的编号（ID）。当推文内容中的字符数严格大于 15 时，该推文是无效的。

以任意顺序返回结果表。


```
-- 查询所有无效推文的编号（内容字符数严格大于 15）
SELECT
    tweet_id
FROM Tweets
WHERE LENGTH(content) > 15;
```
