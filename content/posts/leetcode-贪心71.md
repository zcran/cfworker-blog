---
title: "leetcode-贪心71"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最少侧跳次数

给你一个长度为 n 的 3 跑道道路 ，它总共包含 n + 1 个 点 ，编号为 0 到 n 。一只青蛙从 0 号点第二条跑道 出发 ，它想要跳到点 n 处。然而道路上可能有一些障碍。

给你一个长度为 n + 1 的数组 obstacles ，其中 obstacles[i] （取值范围从 0 到 3）表示在点 i 处的 obstacles[i] 跑道上有一个障碍。如果 obstacles[i] == 0 ，那么点 i 处没有障碍。任何一个点的三条跑道中 最多有一个 障碍。

比方说，如果 obstacles[2] == 1 ，那么说明在点 2 处跑道 1 有障碍。

这只青蛙从点 i 跳到点 i + 1 且跑道不变的前提是点 i + 1 的同一跑道上没有障碍。为了躲避障碍，这只青蛙也可以在 同一个 点处 侧跳 到 另外一条 跑道（这两条跑道可以不相邻），但前提是跳过去的跑道该点处没有障碍。

比方说，这只青蛙可以从点 3 处的跑道 3 跳到点 3 处的跑道 1 。

这只青蛙从点 0 处跑道 2 出发，并想到达点 n 处的 任一跑道 ，请你返回 最少侧跳次数 。

注意：点 0 处和点 n 处的任一跑道都不会有障碍。


```
impl Solution {
    pub fn min_side_jumps(obstacles: Vec<i32>) -> i32 {
        // dp[lane] = 到达当前点该跑道的最少侧跳次数
        // 初始在跑道 2（下标 1），侧跳数为 0；其他跑道需侧跳 1 次
        let mut dp = [1, 0, 1];

        for &obs in &obstacles {
            // 有障碍的跑道设为不可达
            if obs > 0 {
                dp[obs as usize - 1] = i32::MAX / 2;
            }
            // 更新每条跑道的值：从自身或另两条跑道侧跳一次过来
            let (a, b, c) = (dp[0], dp[1], dp[2]);
            for i in 0..3 {
                if obs == i as i32 + 1 {
                    continue;
                }
                let other = match i {
                    0 => b.min(c),
                    1 => a.min(c),
                    _ => a.min(b),
                };
                dp[i] = dp[i].min(other + 1);
            }
        }

        dp[0].min(dp[1]).min(dp[2])
    }
}
```
