---
title: "leetcode-矩阵69"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 统计异或值为给定值的路径数目

给你一个大小为 m x n 的二维整数数组 grid 和一个整数 k 。

你的任务是统计满足以下 条件 且从左上格子 (0, 0) 出发到达右下格子 (m - 1, n - 1) 的路径数目：

每一步你可以向右或者向下走，也就是如果格子存在的话，可以从格子 (i, j) 走到格子 (i, j + 1) 或者格子 (i + 1, j) 。
路径上经过的所有数字 XOR 异或值必须 等于 k 。
请你返回满足上述条件的路径总数。

由于答案可能很大，请你将答案对 10^9 + 7 取余 后返回。


```
impl Solution {
    pub fn count_paths_with_xor_value(grid: Vec<Vec<i32>>, k: i32) -> i32 {
        const MOD: i32 = 1_000_000_007;

        let m = grid.len();
        let n = grid[0].len();

        // 计算所有格子中的最大值，确定异或值的范围上界
        let mx = grid.iter().flatten().copied().max().unwrap();
        // u 是大于 mx 的最小 2 的幂，所有异或结果都 < u
        let u = 1usize << (32 - (mx as u32).leading_zeros()) as usize;
        if k as usize >= u {
            return 0;
        }

        // dp[i][j][x]：从 (0,0) 走到 (i,j)，路径异或值为 x 的方案数
        // 使用三维数组，但按行滚动以节省内存
        let mut dp = vec![vec![0i32; u]; n];

        // 起点
        dp[0][grid[0][0] as usize] = 1;

        for i in 0..m {
            for j in 0..n {
                if i == 0 && j == 0 {
                    continue;
                }
                let val = grid[i][j] as usize;
                let mut curr = vec![0i32; u];
                // 从上方 (i-1, j) 转移
                if i > 0 {
                    for x in 0..u {
                        if dp[j][x] != 0 {
                            let nx = x ^ val;
                            curr[nx] = (curr[nx] + dp[j][x]) % MOD;
                        }
                    }
                }
                // 从左侧 (i, j-1) 转移
                if j > 0 {
                    for x in 0..u {
                        if dp[j - 1][x] != 0 {
                            let nx = x ^ val;
                            curr[nx] = (curr[nx] + dp[j - 1][x]) % MOD;
                        }
                    }
                }
                dp[j] = curr;
            }
        }

        dp[n - 1][k as usize]
    }
}
```
