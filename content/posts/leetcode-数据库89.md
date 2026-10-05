---
title: "leetcode-数据库89"
date: 2026-09-15T10:27:55+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 查找无可用副本的书籍

表：library_books

+------------------+---------+
| Column Name      | Type    |
+------------------+---------+
| book_id          | int     |
| title            | varchar |
| author           | varchar |
| genre            | varchar |
| publication_year | int     |
| total_copies     | int     |
+------------------+---------+
book_id 是这张表的唯一主键。
每一行包含图书馆中一本书的信息，包括图书馆拥有的副本总数。
表：borrowing_records

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| record_id     | int     |
| book_id       | int     |
| borrower_name | varchar |
| borrow_date   | date    |
| return_date   | date    |
+---------------+---------+
record_id 是这张表的唯一主键。
每一行代表一笔借阅交易并且如果这本书目前被借出并且还没有被归还，return_date 为 NULL。
编写一个解决方案以找到 所有 当前被借出（未归还） 且图书馆中 无可用副本 的书籍。

如果存在一条借阅记录，其 return_date 为 NULL，那么这本书被认为 当前是借出的。
返回结果表按当前借阅者数量 降序 排列，然后按书名 升序 排列。


```
-- PostgreSQL 解决方案
-- 目标：找出“当前被借出（未归还）且无可用副本”的书籍
--   当前借出：存在 return_date IS NULL 的借阅记录
--   无可用副本：未归还的借阅数量 = total_copies
-- 返回列：book_id, title, author, genre, publication_year,
--         current_borrowers（当前未归还的借阅数，即 total_copies）
-- 结果按 current_borrowers 降序、title 升序

WITH current_loans AS (
    -- 先聚合出每本书当前未归还的借阅数量
    SELECT
        book_id,
        COUNT(*) AS current_borrowers
    FROM borrowing_records
    WHERE return_date IS NULL
    GROUP BY book_id
)
SELECT
    b.book_id,
    b.title,
    b.author,
    b.genre,
    b.publication_year,
    b.total_copies AS current_borrowers
FROM library_books b
JOIN current_loans cl ON cl.book_id = b.book_id
-- 关键条件：未归还数量等于总副本数，说明无可用副本
WHERE cl.current_borrowers = b.total_copies
ORDER BY
    cl.current_borrowers DESC,
    b.title              ASC;
```
