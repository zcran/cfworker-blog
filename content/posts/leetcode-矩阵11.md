---
title: "leetcode-矩阵11"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 摘樱桃

给你一个 n x n 的网格 grid ，代表一块樱桃地，每个格子由以下三种数字的一种来表示：

0 表示这个格子是空的，所以你可以穿过它。
1 表示这个格子里装着一个樱桃，你可以摘到樱桃然后穿过它。
-1 表示这个格子里有荆棘，挡着你的路。

请你统计并返回：在遵守下列规则的情况下，能摘到的最多樱桃数：

从位置 (0, 0) 出发，最后到达 (n - 1, n - 1) ，只能向下或向右走，并且只能穿越有效的格子（即只可以穿过值为 0 或者 1 的格子）；
当到达 (n - 1, n - 1) 后，你要继续走，直到返回到 (0, 0) ，只能向上或向左走，并且只能穿越有效的格子；
当你经过一个格子且这个格子包含一个樱桃时，你将摘到樱桃并且这个格子会变成空的（值变为 0 ）；
如果在 (0, 0) 和 (n - 1, n - 1) 之间不存在一条可经过的路径，则无法摘到任何一个樱桃。


```
impl Solution {
    pub fn cherry_pickup(grid: Vec<Vec<i32>>) -> i32 {
        let n = grid.len();
        let neg_inf = i32::MIN / 2; // 用足够小的值避免溢出

        // 等价于两个人同时从 (0,0) 走到 (n-1,n-1)，都只向右/下走。
        // 设 k = 步数（即 x1 + y1 = x2 + y2 = k），只需记录两人的行坐标 x1、x2。
        // dp[x1][x2] 表示在某个步数 k 下，两人分别位于 (x1, k-x1) 和 (x2, k-x2) 时的最大樱桃数。
        // 使用一维滚动数组优化空间：只保留上一"步数 k"的状态。
        let mut dp = vec![vec![neg_inf; n]; n];
        dp[0][0] = grid[0][0];

        for k in 1..(2 * n - 1) {
            let mut next = vec![vec![neg_inf; n]; n];
            // 两人的行坐标 x 范围：k - (n-1) <= x <= min(k, n-1)
            let x_min = k.saturating_sub(n - 1);
            let x_max = k.min(n - 1);

            for x1 in x_min..=x_max {
                let y1 = k - x1;
                if grid[x1][y1] == -1 {
                    continue;
                }
                for x2 in x1..=x_max {
                    let y2 = k - x2;
                    if grid[x2][y2] == -1 {
                        continue;
                    }

                    // 从前一步 k-1 的四种走法（右/下）中取最大值
                    let mut best = dp[x1][x2];             // 都往右
                    if x1 > 0 {
                        best = best.max(dp[x1 - 1][x2]);   // 甲往下，乙往右
                    }
                    if x2 > 0 {
                        best = best.max(dp[x1][x2 - 1]);   // 甲往右，乙往下
                    }
                    if x1 > 0 && x2 > 0 {
                        best = best.max(dp[x1 - 1][x2 - 1]); // 都往下
                    }
                    if best == neg_inf {
                        continue;
                    }

                    // 累加当前格子的樱桃；若两人在同一格，只算一次
                    let mut res = best + grid[x1][y1];
                    if x1 != x2 {
                        res += grid[x2][y2];
                    }
                    next[x1][x2] = res;
                }
            }
            dp = next;
        }

        // 若终点不可达，返回 0（题目要求无法摘到樱桃时返回 0）
        dp[n - 1][n - 1].max(0)
    }
}
```
