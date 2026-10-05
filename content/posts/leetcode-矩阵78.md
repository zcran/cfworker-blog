---
title: "leetcode-矩阵78"
date: 2026-09-19T10:41:37+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 网格图中最小异或路径

给你一个大小为 m * n 的二维整数数组 grid。

你从 左上角 的单元格 (0, 0) 出发，想要到达 右下角 的单元格 (m - 1, n - 1)。

在每一步中，你 可以 向右或向下 移动。

路径的 代价 定义为该路径上所有单元格（包括 起点和终点）的值的 按位异或。

返回从 (0, 0) 到 (m - 1, n - 1) 的所有有效路径中 最小 的可能异或值。


```
impl Solution {
    pub fn min_cost(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();

        // 所有元素按位或，异或结果不会超过这个上界
        let or_all = grid.iter().flatten().fold(0, |acc, &x| acc | x) as usize;

        // visited[i][j][x] 表示从 (i, j) 出发、当前异或值为 x 的状态是否已搜索过
        let mut visited = vec![vec![vec![false; or_all + 1]; n]; m];
        let mut ans = i32::MAX;

        // 从终点往回 DFS，xor_val 为从终点到当前位置（不含当前格）的异或值
        fn dfs(
            grid: &Vec<Vec<i32>>,
            visited: &mut Vec<Vec<Vec<bool>>>,
            ans: &mut i32,
            i: usize,
            j: usize,
            xor_val: i32,
            or_all: usize,
        ) {
            // 剪枝：已找到 0（最小可能值）或状态已访问
            if *ans == 0 || visited[i][j][xor_val as usize] {
                return;
            }
            visited[i][j][xor_val as usize] = true;

            let new_xor = xor_val ^ grid[i][j];

            // 到达起点，更新答案
            if i == 0 && j == 0 {
                if new_xor < *ans {
                    *ans = new_xor;
                }
                return;
            }

            // 向上、向左继续搜索
            if i > 0 {
                dfs(grid, visited, ans, i - 1, j, new_xor, or_all);
            }
            if j > 0 {
                dfs(grid, visited, ans, i, j - 1, new_xor, or_all);
            }
        }

        dfs(&grid, &mut visited, &mut ans, m - 1, n - 1, 0, or_all);
        ans
    }
}
```
