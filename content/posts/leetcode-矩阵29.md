---
title: "leetcode-矩阵29"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 摘樱桃 II

给你一个 rows x cols 的矩阵 grid 来表示一块樱桃地。 grid 中每个格子的数字表示你能获得的樱桃数目。

你有两个机器人帮你收集樱桃，机器人 1 从左上角格子 (0,0) 出发，机器人 2 从右上角格子 (0, cols-1) 出发。

请你按照如下规则，返回两个机器人能收集的最多樱桃数目：

从格子 (i,j) 出发，机器人可以移动到格子 (i+1, j-1)，(i+1, j) 或者 (i+1, j+1) 。
当一个机器人经过某个格子时，它会把该格子内所有的樱桃都摘走，然后这个位置会变成空格子，即没有樱桃的格子。
当两个机器人同时到达同一个格子时，它们中只有一个可以摘到樱桃。
两个机器人在任意时刻都不能移动到 grid 外面。
两个机器人最后都要到达 grid 最底下一行。


```
impl Solution {
    pub fn cherry_pickup(grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();

        // dp[j1][j2] 表示在当前行，机器人 1 位于列 j1、机器人 2 位于列 j2 时能收集的最大樱桃数
        // 使用两个二维数组滚动，避免为每一行分配新内存
        let mut dp = vec![vec![-1i32; cols]; cols];
        let mut next = vec![vec![-1i32; cols]; cols];

        // 初始状态：两个机器人都在第 0 行
        dp[0][cols - 1] = grid[0][0] + grid[0][cols - 1];

        for i in 1..rows {
            // 每行开始时重置 next，无需重新分配内存
            for row in next.iter_mut() {
                row.fill(-1);
            }

            for j1 in 0..cols {
                for j2 in 0..cols {
                    // 枚举上一行两个机器人可能的列位置（九种组合）
                    let mut best = -1i32;
                    for dj1 in -1i32..=1 {
                        let pj1 = j1 as i32 + dj1;
                        if pj1 < 0 || pj1 >= cols as i32 {
                            continue;
                        }
                        for dj2 in -1i32..=1 {
                            let pj2 = j2 as i32 + dj2;
                            if pj2 < 0 || pj2 >= cols as i32 {
                                continue;
                            }
                            let prev = dp[pj1 as usize][pj2 as usize];
                            if prev < 0 {
                                continue;
                            }
                            // 同一格子时樱桃只能摘一次
                            let gain = if j1 == j2 {
                                grid[i][j1]
                            } else {
                                grid[i][j1] + grid[i][j2]
                            };
                            best = best.max(prev + gain);
                        }
                    }
                    next[j1][j2] = best;
                }
            }

            std::mem::swap(&mut dp, &mut next);
        }

        // 最后一行所有状态中的最大值即为答案
        dp.iter()
            .flat_map(|row| row.iter())
            .copied()
            .max()
            .unwrap_or(0)
    }
}
```
