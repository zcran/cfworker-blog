---
title: "leetcode-动态规划83"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 粉刷房子 III

在一个小城市里，有 m 个房子排成一排，你需要给每个房子涂上 n 种颜色之一（颜色编号为 1 到 n ）。有的房子去年夏天已经涂过颜色了，所以这些房子不可以被重新涂色。

我们将连续相同颜色尽可能多的房子称为一个街区。（比方说 houses = [1,2,2,3,3,2,1,1] ，它包含 5 个街区  [{1}, {2,2}, {3,3}, {2}, {1,1}] 。）

给你一个数组 houses ，一个 m * n 的矩阵 cost 和一个整数 target ，其中：

houses[i]：是第 i 个房子的颜色，0 表示这个房子还没有被涂色。
cost[i][j]：是将第 i 个房子涂成颜色 j+1 的花费。

请你返回房子涂色方案的最小总花费，使得每个房子都被涂色后，恰好组成 target 个街区。如果没有可用的涂色方案，请返回 -1 。


```
impl Solution {
    pub fn min_cost(houses: Vec<i32>, cost: Vec<Vec<i32>>, m: i32, n: i32, target: i32) -> i32 {
        let m = m as usize;
        let n = n as usize;
        let target = target as usize;
        const INF: i32 = i32::MAX / 2; // 防止加法溢出

        // dp[color][k]：当前房子涂成 color（0-based），已形成 k+1 个街区的最小花费
        let mut dp = vec![vec![INF; target]; n];

        // 初始化第 0 个房子
        if houses[0] != 0 {
            let c = houses[0] as usize - 1;
            dp[c][0] = 0;
        } else {
            for c in 0..n {
                dp[c][0] = cost[0][c];
            }
        }

        // 依次处理后续房子
        for i in 1..m {
            let mut ndp = vec![vec![INF; target]; n];
            let fixed = houses[i] != 0;
            let fixed_color = if fixed { houses[i] as usize - 1 } else { 0 };

            // 遍历当前房子可能涂的颜色
            for color in 0..n {
                if fixed && color != fixed_color {
                    continue;
                }
                let paint_cost = if fixed { 0 } else { cost[i][color] };

                // 从上一状态转移
                for prev_color in 0..n {
                    for k in 0..target {
                        let cur = dp[prev_color][k];
                        if cur == INF {
                            continue;
                        }

                        if prev_color == color {
                            // 同色，街区数不变
                            let val = cur + paint_cost;
                            if val < ndp[color][k] {
                                ndp[color][k] = val;
                            }
                        } else if k + 1 < target {
                            // 异色，街区数 +1
                            let val = cur + paint_cost;
                            if val < ndp[color][k + 1] {
                                ndp[color][k + 1] = val;
                            }
                        }
                    }
                }
            }
            dp = ndp;
        }

        // 取所有颜色中恰好形成 target 个街区的最小花费
        let mut ans = INF;
        for color in 0..n {
            ans = ans.min(dp[color][target - 1]);
        }

        if ans >= INF { -1 } else { ans }
    }
}
```
