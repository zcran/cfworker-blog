---
title: "leetcode-矩阵70"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 使每一列严格递增的最少操作次数

给你一个由 非负 整数组成的 m x n 矩阵 grid。

在一次操作中，你可以将任意元素 grid[i][j] 的值增加 1。

返回使 grid 的所有列 严格递增 所需的 最少 操作次数。


```
impl Solution {
    pub fn minimum_operations(grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();
        let mut ops = 0;

        // 逐列处理，使每列严格递增
        for c in 0..cols {
            // 记录当前列处理到上一行时的实际值（可能已被增大）
            let mut prev = grid[0][c];
            for r in 1..rows {
                // 当前行至少要比上一行大 1
                let required = prev + 1;
                if grid[r][c] < required {
                    // 需要增加的操作次数
                    ops += required - grid[r][c];
                    prev = required;
                } else {
                    // 已经满足严格递增，无需操作
                    prev = grid[r][c];
                }
            }
        }

        ops
    }
}
```
