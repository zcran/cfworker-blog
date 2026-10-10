---
title: "leetcode-动态规划100"
date: 2026-10-05T20:40:00+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 完成比赛的最少时间

给你一个下标从 0 开始的二维整数数组 tires ，其中 tires[i] = [fi, ri] 表示第 i 种轮胎如果连续使用，第 x 圈需要耗时 fi * ri^(x-1) 秒。

比方说，如果 fi = 3 且 ri = 2 ，且一直使用这种类型的同一条轮胎，那么该轮胎完成第 1 圈赛道耗时 3 秒，完成第 2 圈耗时 3 * 2 = 6 秒，完成第 3 圈耗时 3 * 2^2 = 12 秒，依次类推。
同时给你一个整数 changeTime 和一个整数 numLaps 。

比赛总共包含 numLaps 圈，你可以选择 任意 一种轮胎开始比赛。每一种轮胎都有 无数条 。每一圈后，你可以选择耗费 changeTime 秒 换成 任意一种轮胎（也可以换成当前种类的新轮胎）。

请你返回完成比赛需要耗费的 最少 时间。


```
impl Solution {
    pub fn minimum_finish_time(tires: Vec<Vec<i32>>, change_time: i32, num_laps: i32) -> i32 {
        use std::collections::HashMap;
        let n = num_laps as usize;
        let change = change_time as i64;

        // 相同 f 只保留最小的 r（r 越小，后续增长越慢，总是更优）
        let mut best_r: HashMap<i64, i64> = HashMap::new();
        for t in &tires {
            let (f, r) = (t[0] as i64, t[1] as i64);
            best_r.entry(f).and_modify(|e| *e = (*e).min(r)).or_insert(r);
        }

        let min_f = *best_r.keys().min().unwrap();
        // 单圈耗时上限：若某圈耗时超过 (change + min_f)，
        // 则改为换胎 + 跑最快轮胎首圈更划算，无需继续延长该连续段
        let lap_limit = change + min_f;

        // best[x]: 连续不换胎跑 x 圈的最短耗时
        let mut best = vec![i64::MAX; n + 1];
        for (&f, &r) in &best_r {
            let mut lap_time = f;
            let mut total = 0i64;
            for x in 1..=n {
                if lap_time > lap_limit {
                    break;
                }
                total += lap_time;
                if total < best[x] {
                    best[x] = total;
                }
                // 防溢出后计算下一圈耗时
                if lap_time > i64::MAX / r {
                    break;
                }
                lap_time *= r;
            }
        }

        // dp[i]: 完成 i 圈的最小总时间（含中间所有换胎开销）
        // dp[0] 初始为 -change，使第一段开始前无需换胎
        const INF: i64 = i64::MAX / 2;
        let mut dp = vec![INF; n + 1];
        dp[0] = -change;
        for i in 1..=n {
            for x in 1..=i {
                if best[x] == i64::MAX {
                    break; // 更大的 x 也不会被填充
                }
                let cand = dp[i - x] + change + best[x];
                if cand < dp[i] {
                    dp[i] = cand;
                }
            }
        }

        dp[n] as i32
    }
}
```
