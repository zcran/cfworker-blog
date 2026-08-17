---
title: "leetcode-模拟27"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 奇数值单元格的数目

给你一个 m x n 的矩阵，最开始的时候，每个单元格中的值都是 0。

另有一个二维索引数组 indices，indices[i] = [ri, ci] 指向矩阵中的某个位置，其中 ri 和 ci 分别表示指定的行和列（从 0 开始编号）。

对 indices[i] 所指向的每个位置，应同时执行下述增量操作：

1. ri 行上的所有单元格，加 1 。
2. ci 列上的所有单元格，加 1 。

给你 m、n 和 indices 。请你在执行完所有 indices 指定的增量操作后，返回矩阵中 奇数值单元格 的数目。


```
impl Solution {
    /// 返回奇数值单元格的数目
    /// 核心思路：用位掩码记录每行/每列被翻转的次数（奇偶性）
    /// 单元格 (r,c) 的最终值 = row_flips[r] + col_flips[c]，奇偶性由异或决定
    /// 奇数单元格 = (行奇且列偶) + (行偶且列奇)
    pub fn odd_cells(m: i32, n: i32, indices: Vec<Vec<i32>>) -> i32 {
        let mut row_mask = 0u64; // bit i = 1 表示第 i 行被翻转奇数次
        let mut col_mask = 0u64; // bit j = 1 表示第 j 列被翻转奇数次

        for idx in indices {
            row_mask ^= 1 << idx[0];
            col_mask ^= 1 << idx[1];
        }

        let odd_rows = row_mask.count_ones() as i32;
        let odd_cols = col_mask.count_ones() as i32;
        let even_rows = m - odd_rows;
        let even_cols = n - odd_cols;

        // 奇数 = 奇行×偶列 + 偶行×奇列
        odd_rows * even_cols + even_rows * odd_cols
    }
}
```
