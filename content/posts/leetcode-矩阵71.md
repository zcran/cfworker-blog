---
title: "leetcode-矩阵71"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 机器人可以获得的最大金币数

给你一个 m x n 的网格。一个机器人从网格的左上角 (0, 0) 出发，目标是到达网格的右下角 (m - 1, n - 1)。在任意时刻，机器人只能向右或向下移动。

网格中的每个单元格包含一个值 coins[i][j]：

如果 coins[i][j] >= 0，机器人可以获得该单元格的金币。
如果 coins[i][j] < 0，机器人会遇到一个强盗，强盗会抢走该单元格数值的 绝对值 的金币。

机器人有一项特殊能力，可以在行程中 最多感化 2个单元格的强盗，从而防止这些单元格的金币被抢走。

注意：机器人的总金币数可以是负数。

返回机器人在路径上可以获得的 最大金币数 。


```
impl Solution {
    pub fn maximum_amount(coins: Vec<Vec<i32>>) -> i32 {
        let m = coins.len();
        let n = coins[0].len();
        const NEG_INF: i32 = i32::MIN / 2;

        // dp[i][j][k]：到达 (i, j) 且还剩 k 次感化机会时的最大金币数
        // k 取 0..=2
        let mut dp = vec![vec![[NEG_INF; 3]; n]; m];

        for i in 0..m {
            for j in 0..n {
                let val = coins[i][j];

                for k in 0..3 {
                    // 从上方或左方转移过来的最大值
                    let mut best = NEG_INF;

                    if i == 0 && j == 0 {
                        best = 0;
                    } else {
                        if i > 0 {
                            best = best.max(dp[i - 1][j][k]);
                        }
                        if j > 0 {
                            best = best.max(dp[i][j - 1][k]);
                        }
                    }

                    if best == NEG_INF {
                        continue;
                    }

                    // 不使用感化：直接累加当前格的值
                    dp[i][j][k] = dp[i][j][k].max(best + val);

                    // 使用一次感化（仅当当前格为负数且还有机会）
                    if val < 0 && k > 0 {
                        dp[i][j][k - 1] = dp[i][j][k - 1].max(best);
                    }
                }
            }
        }

        // 终点取剩余 0~2 次感化机会中的最大值
        *dp[m - 1][n - 1].iter().max().unwrap()
    }
}
```
