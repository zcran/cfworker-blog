---
title: "leetcode-双指针112"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最少翻转次数使二进制矩阵回文 I

给你一个 m x n 的二进制矩阵 grid 。

如果矩阵中一行或者一列从前往后与从后往前读是一样的，那么我们称这一行或者这一列是 回文 的。

你可以将 grid 中任意格子的值 翻转 ，也就是将格子里的值从 0 变成 1 ，或者从 1 变成 0 。

请你返回 最少 翻转次数，使得矩阵 要么 所有行是 回文的 ，要么所有列是 回文的 。


```
impl Solution {
    /// 使矩阵所有行回文 或 所有列回文 所需的最少翻转次数。
    ///
    /// 思路：
    /// - 让所有行回文：对每一行，检查对称位置 (j, n-1-j)，不同则需翻转 1 次。
    /// - 让所有列回文：对每一列，检查对称位置 (i, m-1-i)，不同则需翻转 1 次。
    /// 两者取较小值即可。
    ///
    /// 时间复杂度 O(m * n)，空间复杂度 O(1)。
    pub fn min_flips(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        let mut row_cnt = 0;
        let mut col_cnt = 0;

        // 统计使所有行回文所需的翻转次数
        for i in 0..m {
            for j in 0..n / 2 {
                if grid[i][j] != grid[i][n - 1 - j] {
                    row_cnt += 1;
                }
            }
        }

        // 统计使所有列回文所需的翻转次数
        for j in 0..n {
            for i in 0..m / 2 {
                if grid[i][j] != grid[m - 1 - i][j] {
                    col_cnt += 1;
                }
            }
        }

        row_cnt.min(col_cnt)
    }
}
```
