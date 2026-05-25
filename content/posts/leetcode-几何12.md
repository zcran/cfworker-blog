---
title: "leetcode-几何12"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 三维形体的表面积

给你一个 n * n 的网格 grid ，上面放置着一些 1 x 1 x 1 的正方体。每个值 v = grid[i][j] 表示 v 个正方体叠放在对应单元格 (i, j) 上。

放置好正方体后，任何直接相邻的正方体都会互相粘在一起，形成一些不规则的三维形体。

请你返回最终这些形体的总表面积。

注意：每个形体的底面也需要计入表面积中。

```
impl Solution {
    pub fn surface_area(grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();

        // 辅助函数：计算单个格子对表面积的贡献
        let cell_contribution = |i: usize, j: usize| {
            let h = grid[i][j];
            if h == 0 { return 0; }
            let up    = if i == 0        { h } else { h - grid[i-1][j].min(h) };
            let down  = if i == rows - 1 { h } else { h - grid[i+1][j].min(h) };
            let left  = if j == 0        { h } else { h - grid[i][j-1].min(h) };
            let right = if j == cols - 1 { h } else { h - grid[i][j+1].min(h) };
            2 + up + down + left + right
        };

        // 生成所有 (i, j) 索引，映射为贡献，最后求和
        (0..rows)
            .flat_map(|i| (0..cols).map(move |j| (i, j)))
            .map(|(i, j)| cell_contribution(i, j))
            .sum()
    }
}
```
