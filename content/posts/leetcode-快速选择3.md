---
title: "Leetcode 快速选择3"
date: 2023-12-19T15:49:31+08:00
tags: ["leetcode", "快速选择"]
draft: false
---

## 找出第 K 大的异或坐标值

给你一个二维矩阵 matrix 和一个整数 k ，矩阵大小为 m x n 由非负整数组成。

矩阵中坐标 (a, b) 的 值 可由对所有满足 0 <= i <= a < m 且 0 <= j <= b < n 的元素 matrix[i][j]（下标从 0 开始计数）执行异或运算得到。

请你找出 matrix 的所有坐标中第 k 大的值（k 的值从 1 开始计数）。

```
impl Solution {
    pub fn kth_largest_value(mut matrix: Vec<Vec<i32>>, k: i32) -> i32 {
        for row in 0..matrix.len() {
            let mut sum = 0;
            for col in 0..matrix[0].len() {
                sum ^= matrix[row][col];
                matrix[row][col] = sum;
            }
        }
        for col in 0..matrix[0].len() {
            let mut sum = 0;
            for row in 0..matrix.len() {
                sum ^= matrix[row][col];
                matrix[row][col] = sum;
            }
        }
        let mut prefix: Vec<i32> = matrix
            .into_iter()
            .map(|row| row.into_iter())
            .flatten()
            .collect();
        let len = prefix.len();
        prefix.select_nth_unstable(len - k as usize);
        prefix[len - k as usize]
    }
}
```