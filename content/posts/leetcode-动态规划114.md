---
title: "leetcode-动态规划114"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 给墙壁刷油漆

给你两个长度为 n 下标从 0 开始的整数数组 cost 和 time ，分别表示给 n 堵不同的墙刷油漆需要的开销和时间。你有两名油漆匠：

一位需要 付费 的油漆匠，刷第 i 堵墙需要花费 time[i] 单位的时间，开销为 cost[i] 单位的钱。

一位 免费 的油漆匠，刷 任意 一堵墙的时间为 1 单位，开销为 0 。但是必须在付费油漆匠 工作 时，免费油漆匠才会工作。

请你返回刷完 n 堵墙最少开销为多少。




```
impl Solution {
    pub fn paint_walls(cost: Vec<i32>, time: Vec<i32>) -> i32 {
        let n = cost.len();
        const INF: i32 = i32::MAX / 2;

        // dp[j]: 当前累计「富余工时」为 j 时的最小开销
        // j 的含义：付费油漆匠累计工作时间内，已安排的免费刷墙数与付费刷墙数之差
        // j 可能为负，用偏移量 n 表示 0，范围 [-n, 2n] 足够覆盖
        let cap = 2 * n + 1;
        let mut dp = vec![INF; cap];
        dp[n] = 0; // 初始富余工时为 0

        for i in 0..n {
            let t = time[i] as usize;
            let c = cost[i];
            let mut ndp = vec![INF; cap];

            for j in 0..cap {
                if dp[j] == INF {
                    continue;
                }
                // 选择一：付费油漆匠刷第 i 堵墙，增加 t 单位富余工时
                let nj = (j + t).min(cap - 1);
                ndp[nj] = ndp[nj].min(dp[j] + c);

                // 选择二：免费油漆匠刷第 i 堵墙，消耗 1 单位富余工时
                if j > 0 {
                    ndp[j - 1] = ndp[j - 1].min(dp[j]);
                }
            }
            dp = ndp;
        }

        // 富余工时 >= n 表示所有墙都已被刷完（免费工时足够覆盖剩余墙）
        *dp.iter().skip(n).min().unwrap()
    }
}
```
