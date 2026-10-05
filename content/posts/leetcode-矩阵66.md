---
title: "leetcode-矩阵66"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 包含所有 1 的最小矩形面积 I

给你一个二维 二进制 数组 grid。请你找出一个边在水平方向和竖直方向上、面积 最小 的矩形，并且满足 grid 中所有的 1 都在矩形的内部。

返回这个矩形可能的 最小 面积。


```
impl Solution {
    pub fn minimum_area(grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();

        // 分别记录所有 1 出现的最小/最大行号和列号
        let (mut min_r, mut max_r) = (rows, 0);
        let (mut min_c, mut max_c) = (cols, 0);

        for (r, row) in grid.iter().enumerate() {
            for (c, &v) in row.iter().enumerate() {
                if v == 1 {
                    min_r = min_r.min(r);
                    max_r = max_r.max(r);
                    min_c = min_c.min(c);
                    max_c = max_c.max(c);
                }
            }
        }

        // 包围所有 1 的最小矩形面积
        ((max_r - min_r + 1) * (max_c - min_c + 1)) as i32
    }
}
```
