---
title: "leetcode-矩阵48"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 判断矩阵是否是一个 X 矩阵

如果一个正方形矩阵满足下述 全部 条件，则称之为一个 X 矩阵 ：

矩阵对角线上的所有元素都 不是 0
矩阵中所有其他元素都是 0

给你一个大小为 n x n 的二维整数数组 grid ，表示一个正方形矩阵。如果 grid 是一个 X 矩阵 ，返回 true ；否则，返回 false 。


```
impl Solution {
    /// X 矩阵：主对角线 + 副对角线上的元素均非 0，其余位置均为 0。
    /// 判断 (i,j) 是否在对角线上：i == j（主）或 i + j == n - 1（副）。
    pub fn check_x_matrix(grid: Vec<Vec<i32>>) -> bool {
        let n = grid.len();
        for i in 0..n {
            for j in 0..n {
                let on_diag = i == j || i + j == n - 1;
                // 对角线元素必须非 0，非对角线元素必须为 0
                if (grid[i][j] == 0) == on_diag {
                    return false;
                }
            }
        }
        true
    }
}
```
