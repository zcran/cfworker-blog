---
title: "leetcode-广度优先搜索41"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 二进制矩阵中翻转最多一次使路径不连通

给你一个下标从 0 开始的 m x n 二进制 矩阵 grid 。你可以从一个格子 (row, col) 移动到格子 (row + 1, col) 或者 (row, col + 1) ，前提是前往的格子值为 1 。如果从 (0, 0) 到 (m - 1, n - 1) 没有任何路径，我们称该矩阵是 不连通 的。

你可以翻转 最多一个 格子的值（也可以不翻转）。你 不能翻转 格子 (0, 0) 和 (m - 1, n - 1) 。

如果可以使矩阵不连通，请你返回 true ，否则返回 false 。

注意 ，翻转一个格子的值，可以使它的值从 0 变 1 ，或从 1 变 0 。


```
impl Solution {
    pub fn is_possible_to_cut_path(grid: Vec<Vec<i32>>) -> bool {
        let (m, n) = (grid.len(), grid[0].len());

        // 起点/终点无法翻转，若矩阵太小也不可能切断
        if m * n <= 2 {
            return false;
        }

        // 正向标记：从 (0,0) 出发能到达的格子
        // 只能向右或向下走，且经过的格子必须为 1
        let mut reachable = vec![vec![false; n]; m];
        reachable[0][0] = true;
        for i in 0..m {
            for j in 0..n {
                if !reachable[i][j] {
                    continue;
                }
                if i + 1 < m && grid[i + 1][j] == 1 {
                    reachable[i + 1][j] = true;
                }
                if j + 1 < n && grid[i][j + 1] == 1 {
                    reachable[i][j + 1] = true;
                }
            }
        }

        // 若本来就不连通，直接返回 true
        if !reachable[m - 1][n - 1] {
            return true;
        }

        // 反向标记：从 (m-1, n-1) 能反向走到（即正向也能到终点）的格子
        // 这里直接复用 reachable，从右下向左上收敛
        for i in (0..m).rev() {
            for j in (0..n).rev() {
                if !reachable[i][j] || i + j == m + n - 2 {
                    continue;
                }
                let can_continue = (i + 1 < m && reachable[i + 1][j])
                    || (j + 1 < n && reachable[i][j + 1]);
                reachable[i][j] = can_continue;
            }
        }

        // 统计每条反对角线（i+j 相同）上有多少条“必经”通路格子
        // 若某条反对角线上只有一个这样的格子，翻转它即可切断所有路径
        let mut count = vec![0i32; m + n - 1];
        for i in 0..m {
            for j in 0..n {
                if reachable[i][j] && i + j < m + n - 2 {
                    count[i + j] += 1;
                }
            }
        }

        // 首尾两条对角线上的格子不能翻转，检查中间的对角线
        count[1..m + n - 2].iter().any(|&c| c == 1)
    }
}
```
