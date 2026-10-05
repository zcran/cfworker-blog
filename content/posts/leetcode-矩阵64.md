---
title: "leetcode-矩阵64"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 判断矩阵是否满足条件

给你一个大小为 m x n 的二维矩阵 grid 。你需要判断每一个格子 grid[i][j] 是否满足：

如果它下面的格子存在，那么它需要等于它下面的格子，也就是 grid[i][j] == grid[i + 1][j] 。
如果它右边的格子存在，那么它需要不等于它右边的格子，也就是 grid[i][j] != grid[i][j + 1] 。

如果 所有 格子都满足以上条件，那么返回 true ，否则返回 false 。


```
impl Solution {
    pub fn satisfies_conditions(grid: Vec<Vec<i32>>) -> bool {
        let rows = grid.len();
        let cols = grid[0].len();

        for i in 0..rows {
            for j in 0..cols {
                // 下方相邻必须相等
                if i + 1 < rows && grid[i][j] != grid[i + 1][j] {
                    return false;
                }
                // 右方相邻必须不等
                if j + 1 < cols && grid[i][j] == grid[i][j + 1] {
                    return false;
                }
            }
        }

        true
    }
}
```
