---
title: "leetcode-数据库79"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 首字母大写 II

表：user_content

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| content_id   | int     |
| content_text | varchar |
+--------------+---------+
content_id 是该表的唯一键。
每一行包含一个唯一的 ID 以及对应的文本内容。
单词是指不包含空格的极大非空字符序列。

编写一个解决方案，按照以下规则对 content_text 列中的文本进行转换，并对每个单词应用这些规则：

如果单词以一个不是英文字母的字符开头，则保持整个单词不变。
否则，如果单词由两个或更多非空的英文字母部分通过连字符 - 连接而成，则将每个部分的首字母转换为大写，并将每个部分的其余字母转换为小写。例如，top-rated 转换为 Top-Rated，FR-ONT-end 转换为 Fr-Ont-End。
否则，将单词的首字母转换为大写，并将其余所有英文字母转换为小写。所有特殊字符保持不变。
所有其他格式和空格必须保持不变。

返回结果表，其中同时包含原始的 content_text 以及按照上述规则转换后的文本。


```
-- PostgreSQL 解决方案
-- 规则：按空格拆词，对每个词判断是否需按连字符分段转换
-- 1) 非字母开头 → 原样保留
-- 2) 字母开头且含 '-' 分段 → 每段首字母大写、其余小写
-- 3) 字母开头无 '-' → 首字母大写、其余小写
-- 保留原始空格与特殊字符

WITH words AS (
    -- 按空格切分每个 content_text，同时保留单词序号以还原顺序
    SELECT
        u.content_id,
        u.content_text AS original_text,
        w.ord,
        w.word
    FROM user_content u
    CROSS JOIN LATERAL (
        SELECT
            ord,
            word
        FROM unnest(regexp_split_to_array(u.content_text, ' ')) WITH ORDINALITY AS t(word, ord)
    ) w
),
transformed AS (
    SELECT
        content_id,
        original_text,
        ord,
        CASE
            -- 规则 1：首字符非英文字母 → 原样保留
            WHEN word !~ '^[A-Za-z]' THEN word
            -- 规则 2：字母开头且含有 '-' 分段（所有段均为非空字母）
            WHEN word ~ '^[A-Za-z]+(-[A-Za-z]+)+$' THEN (
                SELECT string_agg(
                           upper(left(seg, 1)) || lower(substr(seg, 2)),
                           '-'
                           ORDER BY seg_ord
                       )
                FROM unnest(regexp_split_to_array(word, '-')) WITH ORDINALITY AS s(seg, seg_ord)
            )
            -- 规则 3：字母开头普通单词 → 首字母大写，其余小写
            ELSE upper(left(word, 1)) || lower(substr(word, 2))
        END AS new_word
    FROM words
)
SELECT
    content_id,
    original_text,
    -- 按原顺序用空格拼回；空字符串单词也能保持原有空格结构
    string_agg(new_word, ' ' ORDER BY ord) AS converted_text
FROM transformed
GROUP BY content_id, original_text
ORDER BY content_id;
```
