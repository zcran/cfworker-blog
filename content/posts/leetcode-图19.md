---
title: "leetcode-图19"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 规定时间内到达终点的最小花费

一个国家有 n 个城市，城市编号为 0 到 n - 1 ，题目保证 所有城市 都由双向道路 连接在一起 。道路由二维整数数组 edges 表示，其中 edges[i] = [xi, yi, timei] 表示城市 xi 和 yi 之间有一条双向道路，耗费时间为 timei 分钟。两个城市之间可能会有多条耗费时间不同的道路，但是不会有道路两头连接着同一座城市。

每次经过一个城市时，你需要付通行费。通行费用一个长度为 n 且下标从 0 开始的整数数组 passingFees 表示，其中 passingFees[j] 是你经过城市 j 需要支付的费用。

一开始，你在城市 0 ，你想要在 maxTime 分钟以内 （包含 maxTime 分钟）到达城市 n - 1 。旅行的 费用 为你经过的所有城市 通行费之和 （包括 起点和终点城市的通行费）。

给你 maxTime，edges 和 passingFees ，请你返回完成旅行的 最小费用 ，如果无法在 maxTime 分钟以内完成旅行，请你返回 -1 。


```
impl Solution {
    pub fn min_cost(max_time: i32, edges: Vec<Vec<i32>>, passing_fees: Vec<i32>) -> i32 {
        let n = passing_fees.len();
        let max_time = max_time as usize;

        // dp[t][v]：在恰好 t 分钟到达城市 v 的最小费用
        let mut dp = vec![vec![i32::MAX; n]; max_time + 1];
        dp[0][0] = passing_fees[0];

        // 按时间递增进行 DP
        for t in 1..=max_time {
            for edge in &edges {
                let (u, v, w) = (edge[0] as usize, edge[1] as usize, edge[2] as usize);
                if w > t { continue; }

                // 从 u 到 v 或从 v 到 u
                if dp[t - w][u] != i32::MAX {
                    dp[t][v] = dp[t][v].min(dp[t - w][u] + passing_fees[v]);
                }
                if dp[t - w][v] != i32::MAX {
                    dp[t][u] = dp[t][u].min(dp[t - w][v] + passing_fees[u]);
                }
            }
        }

        // 取所有不超过 max_time 的方案中的最小值
        let mut ans = i32::MAX;
        for t in 0..=max_time {
            ans = ans.min(dp[t][n - 1]);
        }

        if ans == i32::MAX { -1 } else { ans }
    }
}
```
