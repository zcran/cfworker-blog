---
title: "leetcode-矩阵19"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 下降路径最小和

给你一个 n x n 的 方形 整数数组 matrix ，请你找出并返回通过 matrix 的下降路径 的 最小和 。

下降路径 可以从第一行中的任何元素开始，并从每一行中选择一个元素。在下一行选择的元素和当前行所选元素最多相隔一列（即位于正下方或者沿对角线向左或者向右的第一个元素）。具体来说，位置 (row, col) 的下一个元素应当是 (row + 1, col - 1)、(row + 1, col) 或者 (row + 1, col + 1) 。


```
impl Solution {
    pub fn min_falling_path_sum(matrix: Vec<Vec<i32>>) -> i32 {
        let n = matrix.len();

        // 一维滚动数组：dp[j] 表示到达当前行第 j 列的最小下降路径和。
        // 初始为第一行的值。
        let mut dp = matrix[0].clone();

        for i in 1..n {
            let mut next = vec![0; n];
            for j in 0..n {
                // 上一行可达列：j-1、j、j+1（越界忽略）
                let mut mn = dp[j];
                if j > 0 {
                    mn = mn.min(dp[j - 1]);
                }
                if j + 1 < n {
                    mn = mn.min(dp[j + 1]);
                }
                next[j] = mn + matrix[i][j];
            }
            dp = next;
        }

        // 最后一行的最小值即为答案
        *dp.iter().min().unwrap()
    }
}

```
