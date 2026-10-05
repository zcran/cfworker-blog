---
title: "leetcode-矩阵20"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 按列翻转得到最大值等行数

给定 m x n 矩阵 matrix 。

你可以从中选出任意数量的列并翻转其上的 每个 单元格。（即翻转后，单元格的值从 0 变成 1，或者从 1 变为 0 。）

返回 经过一些翻转后，行内所有值都相等的最大行数 。


```
use std::collections::HashMap;

impl Solution {
    pub fn max_equal_rows_after_flips(matrix: Vec<Vec<i32>>) -> i32 {
        let n = matrix[0].len();
        let mut freq: HashMap<Vec<i32>, i32> = HashMap::new();

        // 关键观察：两行能通过翻转相同的列集合变为全等，当且仅当
        // 它们的"模式"相同（即每列与首列的关系一致）。
        // 把每行归一化：与首列相同的记为 0，不同的记为 1。
        // 归一化后相同模式的行，可以一起变为全等，因此取最大频次。
        for row in &matrix {
            let first = row[0];
            let pattern: Vec<i32> = row.iter().map(|&x| x ^ first).collect();
            *freq.entry(pattern).or_insert(0) += 1;
        }

        // 也可以用字节串作为 key 以减少内存，这里用 Vec<i32> 更直观。
        freq.values().copied().max().unwrap_or(0)
    }
}
```
