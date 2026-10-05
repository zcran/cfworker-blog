---
title: "leetcode-矩阵12"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 托普利茨矩阵

给你一个 m x n 的矩阵 matrix 。如果这个矩阵是托普利茨矩阵，返回 true ；否则，返回 false 。

如果矩阵上每一条由左上到右下的对角线上的元素都相同，那么这个矩阵是 托普利茨矩阵 。


```
impl Solution {
    pub fn is_toeplitz_matrix(matrix: Vec<Vec<i32>>) -> bool {
        let m = matrix.len();
        let n = matrix[0].len();

        // 托普利茨矩阵的充要条件：每个元素都等于其左上方元素。
        // 从第 2 行第 2 列开始检查即可，跳过第 0 行和第 0 列。
        for i in 1..m {
            for j in 1..n {
                if matrix[i][j] != matrix[i - 1][j - 1] {
                    return false;
                }
            }
        }

        true
    }
}
```
