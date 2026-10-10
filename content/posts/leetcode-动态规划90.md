---
title: "leetcode-动态规划90"
date: 2026-10-05T20:40:00+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 准时抵达会议现场的最小跳过休息次数

给你一个整数 hoursBefore ，表示你要前往会议所剩下的可用小时数。要想成功抵达会议现场，你必须途经 n 条道路。道路的长度用一个长度为 n 的整数数组 dist 表示，其中 dist[i] 表示第 i 条道路的长度（单位：千米）。另给你一个整数 speed ，表示你在道路上前进的速度（单位：千米每小时）。

当你通过第 i 条路之后，就必须休息并等待，直到 下一个整数小时 才能开始继续通过下一条道路。注意：你不需要在通过最后一条道路后休息，因为那时你已经抵达会议现场。

例如，如果你通过一条道路用去 1.4 小时，那你必须停下来等待，到 2 小时才可以继续通过下一条道路。如果通过一条道路恰好用去 2 小时，就无需等待，可以直接继续。

然而，为了能准时到达，你可以选择 跳过 一些路的休息时间，这意味着你不必等待下一个整数小时。注意，这意味着与不跳过任何休息时间相比，你可能在不同时刻到达接下来的道路。

例如，假设通过第 1 条道路用去 1.4 小时，且通过第 2 条道路用去 0.6 小时。跳过第 1 条道路的休息时间意味着你将会在恰好 2 小时完成通过第 2 条道路，且你能够立即开始通过第 3 条道路。

返回准时抵达会议现场所需要的 最小跳过次数 ，如果 无法准时参会 ，返回 -1 。


```
impl Solution {
    pub fn min_skips(dist: Vec<i32>, speed: i32, hours_before: i32) -> i32 {
        let n = dist.len();
        let speed = speed as i64;
        // 将时间限制转换为“距离时间”单位（实际时间 * speed），避免浮点误差
        let limit = hours_before as i64 * speed;
        const INF: i64 = i64::MAX / 2;

        // dp[j]: 处理完当前道路后，跳过 j 次休息所花费的最小累计时间（以距离为单位）
        // 即实际时间 * speed。初始时未处理任何道路，跳过 0 次，时间为 0
        let mut dp = vec![INF; n + 1];
        dp[0] = 0;

        for i in 0..n {
            let d = dist[i] as i64;
            let is_last = i == n - 1; // 最后一条道路后无需休息
            let mut ndp = vec![INF; n + 1];

            for j in 0..=i {
                if dp[j] == INF {
                    continue;
                }

                // 选择 1：不跳过当前道路后的休息
                let mut t = dp[j] + d;
                if !is_last {
                    // 等待到下一个整数小时：向上取整到 speed 的倍数
                    t = ((t + speed - 1) / speed) * speed;
                }
                if t < ndp[j] {
                    ndp[j] = t;
                }

                // 选择 2：跳过当前道路后的休息（仅对非最后一条道路有意义）
                if !is_last {
                    let t_skip = dp[j] + d; // 跳过休息，直接累加精确耗时
                    if t_skip < ndp[j + 1] {
                        ndp[j + 1] = t_skip;
                    }
                }
            }
            dp = ndp;
        }

        // 寻找满足时间限制的最小跳过次数
        for j in 0..=n {
            if dp[j] <= limit {
                return j as i32;
            }
        }
        -1
    }
}
```
