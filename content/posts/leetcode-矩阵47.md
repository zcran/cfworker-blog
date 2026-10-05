---
title: "leetcode-矩阵47"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 网格中的最小路径代价

给你一个下标从 0 开始的整数矩阵 grid ，矩阵大小为 m x n ，由从 0 到 m * n - 1 的不同整数组成。你可以在此矩阵中，从一个单元格移动到 下一行 的任何其他单元格。如果你位于单元格 (x, y) ，且满足 x < m - 1 ，你可以移动到 (x + 1, 0), (x + 1, 1), ..., (x + 1, n - 1) 中的任何一个单元格。注意： 在最后一行中的单元格不能触发移动。

每次可能的移动都需要付出对应的代价，代价用一个下标从 0 开始的二维数组 moveCost 表示，该数组大小为 (m * n) x n ，其中 moveCost[i][j] 是从值为 i 的单元格移动到下一行第 j 列单元格的代价。从 grid 最后一行的单元格移动的代价可以忽略。

grid 一条路径的代价是：所有路径经过的单元格的 值之和 加上 所有移动的 代价之和 。从 第一行 任意单元格出发，返回到达 最后一行 任意单元格的最小路径代价。


```
impl Solution {
    /// 自底向上原地 DP：
    ///   grid[i][j] 更新为「从 (i,j) 出发到达最后一行的最小路径代价」。
    ///   转移：grid[i][j] = grid[i][j] + min over k ( grid[i+1][k] + move_cost[原值][k] )
    ///
    /// 关键点：grid[i][j] 的“原值”必须先用于查 move_cost，再被覆盖。
    /// 由于是原地覆盖且只依赖下一行，空间 O(1)（除输入外）。
    ///
    /// 时间复杂度 O(m*n²)，空间 O(1)。
    pub fn min_path_cost(mut grid: Vec<Vec<i32>>, move_cost: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();

        for i in (0..m - 1).rev() {
            for j in 0..n {
                // 先取原值（未覆盖前）用于查 move_cost 行
                let val = grid[i][j] as usize;
                // 下一行各列的 (到达代价 + 移动代价) 取最小
                let best = (0..n)
                    .map(|k| grid[i + 1][k] + move_cost[val][k])
                    .min()
                    .unwrap();
                grid[i][j] += best;
            }
        }

        // 第一行任意列出发的最小代价
        grid[0].iter().copied().min().unwrap()
    }
}
```

zip 改 (0..n).map：zip 依赖两个迭代器等长，语义上略隐晦；(0..n) 索引更直接，且 move_cost[val][k] 与 grid[i+1][k] 对应关系一目了然。

copied() 替代解引用：grid[0].iter().copied().min() 比 *grid[0].iter().min().unwrap() 少一次 unwrap 前的解引用，语义更顺。
