---
title: "leetcode-矩阵49"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 矩阵中的局部最大值

给你一个大小为 n x n 的整数矩阵 grid 。

生成一个大小为 (n - 2) x (n - 2) 的整数矩阵  maxLocal ，并满足：

maxLocal[i][j] 等于 grid 中以 i + 1 行和 j + 1 列为中心的 3 x 3 矩阵中的 最大值 。

换句话说，我们希望找出 grid 中每个 3 x 3 矩阵中的最大值。

返回生成的矩阵。


```
impl Solution {
    /// 对每个以 (i+1, j+1) 为中心的 3×3 子矩阵取最大值，输出 (n-2)×(n-2) 矩阵。
    /// 用行切片 + `iter().max()` 遍历 3 行，比手写 9 次 max 更简洁不易错。
    pub fn largest_local(grid: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let n = grid.len();
        let mut ret = vec![vec![0; n - 2]; n - 2];
        for i in 0..n - 2 {
            for j in 0..n - 2 {
                // 中心为 (i+1, j+1)，三行分别为 i, i+1, i+2，列范围 j..j+3
                ret[i][j] = (i..i + 3)
                    .flat_map(|r| &grid[r][j..j + 3])
                    .copied()
                    .max()
                    .unwrap();
            }
        }
        ret
    }
}
```
