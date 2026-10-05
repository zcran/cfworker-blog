---
title: "leetcode-矩阵32"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 二进制矩阵中的特殊位置

给定一个 m x n 的二进制矩阵 mat，返回矩阵 mat 中特殊位置的数量。

如果位置 (i, j) 满足 mat[i][j] == 1 并且行 i 与列 j 中的所有其他元素都是 0（行和列的下标从 0 开始计数），那么它被称为 特殊 位置。


```
impl Solution {
    pub fn num_special(mat: Vec<Vec<i32>>) -> i32 {
        let rows = mat.len();
        let cols = mat[0].len();

        // 统计每一行和每一列中 1 的个数
        let mut row_sum = vec![0i32; rows];
        let mut col_sum = vec![0i32; cols];
        for i in 0..rows {
            for j in 0..cols {
                if mat[i][j] == 1 {
                    row_sum[i] += 1;
                    col_sum[j] += 1;
                }
            }
        }

        // 特殊位置：该格子为 1，且所在行、列都恰好只有这一个 1
        let mut count = 0;
        for i in 0..rows {
            for j in 0..cols {
                if mat[i][j] == 1 && row_sum[i] == 1 && col_sum[j] == 1 {
                    count += 1;
                }
            }
        }

        count
    }
}
```
