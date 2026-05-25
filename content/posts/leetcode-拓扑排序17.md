---
title: "leetcode-拓扑排序17"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 网格图中递增路径的数目

给你一个 m x n 的整数网格图 grid ，你可以从一个格子移动到 4 个方向相邻的任意一个格子。

请你返回在网格图中从 任意 格子出发，达到 任意 格子，且路径中的数字是 严格递增 的路径数目。由于答案可能会很大，请将结果对 109 + 7 取余 后返回。

如果两条路径中访问过的格子不是完全相同的，那么它们视为两条不同的路径。

```
impl Solution {
    /// 计算网格中所有严格递增路径的总数（对 1e9+7 取模）。
    /// 路径定义：从任一格子出发，每次可向上/下/左/右移动到数值更大的相邻格子。
    /// 长度为 1 的路径（单格子）也算在内。
    pub fn count_paths(grid: Vec<Vec<i32>>) -> i32 {
        const MOD: u64 = 1_000_000_007;
        let m = grid.len();
        let n = grid[0].len();

        // dp[i][j] = 以 (i,j) 为起点的严格递增路径总数（包含自身）
        let mut dp = vec![vec![1; n]; m];

        // 收集所有格子的 (值, 行, 列) 并按值升序排序
        // 注意：使用 `&grid` 引用，避免所有权转移
        let grid_ref = &grid;
        let mut cells: Vec<_> = (0..m)
            .flat_map(|i| (0..n).map(move |j| (grid_ref[i][j], i, j)))
            .collect();
        cells.sort_unstable_by_key(|&(val, _, _)| val);

        // 按值从小到大处理（数值小的格子先更新，保证后续依赖正确）
        for &(_, i, j) in &cells {
            let val = grid[i][j];   // grid 仍可用，未被移动
            // 计算四个方向上数值更小的邻居的路径数之和
            let sum_smaller = [(i as i32 - 1, j as i32),
                               (i as i32 + 1, j as i32),
                               (i as i32, j as i32 - 1),
                               (i as i32, j as i32 + 1)]
                .iter()
                .filter_map(|&(x, y)| {
                    if x >= 0 && x < m as i32 && y >= 0 && y < n as i32 {
                        let (nx, ny) = (x as usize, y as usize);
                        if grid[nx][ny] < val {
                            Some(dp[nx][ny])
                        } else {
                            None
                        }
                    } else {
                        None
                    }
                })
                .sum::<u64>();
            // 更新 dp[i][j]，注意取模
            dp[i][j] = (dp[i][j] + sum_smaller) % MOD;
        }

        // 所有 dp 值之和即为答案，再次取模后转为 i32
        let total = dp.iter().flat_map(|row| row.iter()).sum::<u64>() % MOD;
        total as i32
    }
}
```
