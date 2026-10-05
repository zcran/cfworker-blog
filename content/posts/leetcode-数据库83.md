---
title: "leetcode-数据库83"
date: 2026-09-15T10:27:54+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 查找具有有效序列号的产品

表：products

+--------------+------------+
| Column Name  | Type       |
+--------------+------------+
| product_id   | int        |
| product_name | varchar    |
| description  | varchar    |
+--------------+------------+
(product_id) 是这张表的唯一主键。
这张表的每一行表示一个产品的唯一 ID，名字和描述。
编写一个解决方案来找到所有描述中 包含一个有效序列号 模式的产品。一个有效序列号符合下述规则：

以 SN 字母开头（区分大小写）。
后面有恰好 4 位数字。
接着是一个短横（-）， 短横后面还有另一组 4 位数字
序列号必须在描述内（可能不在描述的开头）
返回结果表以 product_id 升序 排序。


```
-- PostgreSQL 解决方案
-- 有效序列号：以 SN 开头（区分大小写），紧跟 4 位数字，一个 '-'，再 4 位数字
-- 形如 SN1234-5678，可出现在描述中任意位置，但左右不能紧邻字母/数字
-- 用 POSIX 正则 ~（区分大小写）匹配，结果按 product_id 升序

SELECT
    product_id,
    product_name,
    description
FROM products
WHERE description ~ '(^|[^A-Za-z0-9])SN[0-9]{4}-[0-9]{4}([^A-Za-z0-9]|$)'
ORDER BY product_id ASC;
```
