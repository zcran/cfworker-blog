---
title: "leetcode-模拟18"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 转置矩阵

给你一个二维整数数组 matrix， 返回 matrix 的 转置矩阵 。

矩阵的 转置 是指将矩阵的主对角线翻转，交换矩阵的行索引与列索引。


```
impl Solution {
    /// 矩阵转置：交换行索引与列索引
    /// 时间复杂度: O(m*n)  空间复杂度: O(m*n) — 返回矩阵必需
    pub fn transpose(matrix: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let rows = matrix.len();
        let cols = matrix[0].len();

        // 预分配结果矩阵：原 m×n → 转置后 n×m
        let mut result = vec![Vec::with_capacity(rows); cols];

        for row in &matrix {
            for (j, &val) in row.iter().enumerate() {
                result[j].push(val);
            }
        }

        result
    }
}
```
