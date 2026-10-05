---
title: "leetcode-矩阵72"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 按对角线进行矩阵排序

给你一个大小为 n x n 的整数方阵 grid。返回一个经过如下调整的矩阵：

左下角三角形（包括中间对角线）的对角线按 非递增顺序 排序。
右上角三角形 的对角线按 非递减顺序 排序。


```
impl Solution {
    pub fn sort_matrix(mut grid: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let n = grid.len();

        // 1) 处理主对角线及左下角三角形：
        //    对角线从 (i, 0) 出发（i = 0..n-1），按非递增顺序排序。
        for i in 0..n {
            let len = n - i;
            // 收集该对角线上的元素
            let mut diag: Vec<i32> = (0..len).map(|k| grid[i + k][k]).collect();
            // 非递增排序
            diag.sort_unstable_by(|a, b| b.cmp(a));
            // 写回
            for k in 0..len {
                grid[i + k][k] = diag[k];
            }
        }

        // 2) 处理右上角三角形（不含主对角线）：
        //    对角线从 (0, j) 出发（j = 1..n-1），按非递减顺序排序。
        for j in 1..n {
            let len = n - j;
            // 收集该对角线上的元素
            let mut diag: Vec<i32> = (0..len).map(|k| grid[k][j + k]).collect();
            // 非递减排序
            diag.sort_unstable();
            // 写回
            for k in 0..len {
                grid[k][j + k] = diag[k];
            }
        }

        grid
    }
}
```
