---
title: "leetcode-矩阵25"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 最大得分的路径数目

给你一个正方形字符数组 board ，你从数组最右下方的字符 'S' 出发。

你的目标是到达数组最左上角的字符 'E' ，数组剩余的部分为数字字符 1, 2, ..., 9 或者障碍 'X'。在每一步移动中，你可以向上、向左或者左上方移动，可以移动的前提是到达的格子没有障碍。

一条路径的 「得分」 定义为：路径上所有数字的和。

请你返回一个列表，包含两个整数：第一个整数是 「得分」 的最大值，第二个整数是得到最大得分的方案数，请把结果对 10^9 + 7 取余。

如果没有任何路径可以到达终点，请返回 [0, 0] 。


```
impl Solution {
    pub fn paths_with_max_score(board: Vec<String>) -> Vec<i32> {
        const MOD: i32 = 1_000_000_007;
        let n = board.len();
        // 将字符网格转为字节网格，避免 char 的处理开销
        let grid: Vec<Vec<u8>> = board.iter().map(|s| s.as_bytes().to_vec()).collect();

        // dp[i][j] = (到达 (i,j) 的最大得分, 对应方案数)
        // 从右下角出发，逆序遍历，起点得分设为 0，方案数为 1
        let mut dp = vec![vec![(-1i32, 0i32); n]; n];
        dp[n - 1][n - 1] = (0, 1);

        // 从右下往左上遍历，每个格子只需看右、下、右下三个邻居
        for i in (0..n).rev() {
            for j in (0..n).rev() {
                // 起点已初始化，障碍直接跳过
                if (i == n - 1 && j == n - 1) || grid[i][j] == b'X' {
                    continue;
                }

                // 从三个可达方向中取最优，得分相同时累加方案数
                for &(u, v) in &[(i + 1, j), (i, j + 1), (i + 1, j + 1)] {
                    if u >= n || v >= n {
                        continue;
                    }
                    let (score, cnt) = dp[u][v];
                    if score < 0 {
                        continue;
                    }
                    if score > dp[i][j].0 {
                        dp[i][j] = (score, cnt);
                    } else if score == dp[i][j].0 {
                        dp[i][j].1 = (dp[i][j].1 + cnt) % MOD;
                    }
                }

                // 加上当前格子的数字（起点 'S' 和终点 'E' 不加分）
                if dp[i][j].0 >= 0 {
                    let c = grid[i][j];
                    if c.is_ascii_digit() {
                        dp[i][j].0 += (c - b'0') as i32;
                    }
                }
            }
        }

        if dp[0][0].0 >= 0 {
            vec![dp[0][0].0, dp[0][0].1]
        } else {
            vec![0, 0]
        }
    }
}
```
