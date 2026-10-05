---
title: "leetcode-矩阵75"
date: 2026-09-19T10:41:37+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 带传送的最小路径成本

给你一个 m x n 的二维整数数组 grid 和一个整数 k。你从左上角的单元格 (0, 0) 出发，目标是到达右下角的单元格 (m - 1, n - 1)。

有两种移动方式可用：

普通移动：你可以从当前单元格 (i, j) 向右或向下移动，即移动到 (i, j + 1)（右）或 (i + 1, j)（下）。成本为目标单元格的值。

传送：你可以从任意单元格 (i, j) 传送到任意满足 grid[x][y] <= grid[i][j] 的单元格 (x, y)；此移动的成本为 0。你最多可以传送 k 次。

返回从 (0, 0) 到达单元格 (m - 1, n - 1) 的 最小 总成本。


```
impl Solution {
    pub fn min_cost(grid: Vec<Vec<i32>>, k: i32) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        let inf = i32::MAX / 2;

        // dp[i][j] 表示到达 (i, j) 的最小成本
        let mut dp = vec![vec![inf; n]; m];
        dp[0][0] = 0;

        // 预处理所有格子坐标，按 grid 值降序排列，供传送步骤使用
        let mut points: Vec<(usize, usize)> = (0..m)
            .flat_map(|i| (0..n).map(move |j| (i, j)))
            .collect();
        points.sort_unstable_by_key(|&(i, j)| std::cmp::Reverse(grid[i][j]));

        // 最多使用 k 次传送，因此迭代 k+1 次：
        // t=0 只进行普通移动；t>0 先传送再普通移动
        for t in 0..=k {
            // 传送：从值较大的格子可以 0 成本到达值较小的格子
            if t > 0 {
                let mut min_cost = inf;
                let mut start = 0;
                while start < points.len() {
                    let val = grid[points[start].0][points[start].1];
                    let mut end = start;
                    let mut group_min = inf;

                    // 找出所有值等于 val 的格子
                    while end < points.len() && grid[points[end].0][points[end].1] == val {
                        let (r, c) = points[end];
                        group_min = group_min.min(dp[r][c]);
                        end += 1;
                    }

                    // 从所有值 >= val 的格子传送过来的最小成本
                    min_cost = min_cost.min(group_min);

                    // 更新该组所有格子的 dp
                    for idx in start..end {
                        let (r, c) = points[idx];
                        dp[r][c] = dp[r][c].min(min_cost);
                    }
                    start = end;
                }
            }

            // 普通移动：向右或向下，成本为目标格子的值
            for i in 0..m {
                for j in 0..n {
                    if i == 0 && j == 0 {
                        continue;
                    }
                    if i > 0 {
                        dp[i][j] = dp[i][j].min(dp[i - 1][j] + grid[i][j]);
                    }
                    if j > 0 {
                        dp[i][j] = dp[i][j].min(dp[i][j - 1] + grid[i][j]);
                    }
                }
            }
        }

        dp[m - 1][n - 1]
    }
}
```
