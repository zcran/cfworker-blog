---
title: "leetcode-递归22"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 第K个语法符号


我们构建了一个包含 n 行( 索引从 1  开始 )的表。首先在第一行我们写上一个 0。接下来的每一行，将前一行中的0替换为01，1替换为10。

例如，对于 n = 3 ，第 1 行是 0 ，第 2 行是 01 ，第3行是 0110 。

给定行数 n 和序数 k，返回第 n 行中第 k 个字符。（ k 从索引 1 开始）

```
impl Solution {
    pub fn kth_grammar(n: i32, k: i32) -> i32 {
        (k - 1).count_ones() as i32 & 1
    }
}
```
