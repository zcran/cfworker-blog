---
title: "leetcode-动态规划93"
date: 2026-10-05T20:40:00+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 用三种不同颜色为网格涂色

给你两个整数 m 和 n 。构造一个 m x n 的网格，其中每个单元格最开始是白色。请你用 红、绿、蓝 三种颜色为每个单元格涂色。所有单元格都需要被涂色。

涂色方案需要满足：不存在相邻两个单元格颜色相同的情况 。返回网格涂色的方法数。因为答案可能非常大， 返回 对 10^9 + 7 取余 的结果。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn color_the_grid(m: i32, n: i32) -> i32 {
        // 保证列数 m 较小，减少状态数
        let (m, n) = if m <= n {
            (m as usize, n as usize)
        } else {
            (n as usize, m as usize)
        };

        // 1. 枚举所有合法行状态：相邻格子颜色不同
        // 状态用三进制编码，每位 0/1/2 表示红/绿/蓝
        let mask_end = 3usize.pow(m as u32);
        let mut colors: Vec<Vec<u8>> = Vec::new();
        for mask in 0..mask_end {
            let mut cur = Vec::with_capacity(m);
            let mut x = mask;
            let mut ok = true;
            for _ in 0..m {
                cur.push((x % 3) as u8);
                x /= 3;
            }
            for i in 0..m - 1 {
                if cur[i] == cur[i + 1] {
                    ok = false;
                    break;
                }
            }
            if ok {
                colors.push(cur);
            }
        }

        // 2. 预处理相容矩阵：两行同一列颜色均不同
        let s = colors.len();
        let mut compat = vec![Vec::new(); s];
        for i in 0..s {
            for j in 0..s {
                if (0..m).all(|k| colors[i][k] != colors[j][k]) {
                    compat[i].push(j);
                }
            }
        }

        // 3. DP：dp[i] 表示当前列以第 i 种行状态结尾的方案数
        let mut dp = vec![1i64; s];
        for _ in 1..n {
            let mut ndp = vec![0i64; s];
            for i in 0..s {
                let mut sum = 0i64;
                for &j in &compat[i] {
                    sum += dp[j];
                }
                ndp[i] = sum % MOD;
            }
            dp = ndp;
        }

        // 4. 汇总所有行状态的方案数
        let mut ans = 0i64;
        for &v in &dp {
            ans = (ans + v) % MOD;
        }
        ans as i32
    }
}
```
