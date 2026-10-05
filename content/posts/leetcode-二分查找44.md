---
title: "leetcode-二分查找44"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 最多可以参加的会议数目 II


给你一个 events 数组，其中 events[i] = [startDayi, endDayi, valuei] ，表示第 i 个会议在 startDayi 天开始，第 endDayi 天结束，如果你参加这个会议，你能得到价值 valuei 。同时给你一个整数 k 表示你能参加的最多会议数目。

你同一时间只能参加一个会议。如果你选择参加某个会议，那么你必须 完整 地参加完这个会议。会议结束日期是包含在会议内的，也就是说你不能同时参加一个开始日期与另一个结束日期相同的两个会议。

请你返回能得到的会议价值 最大和 。

```
impl Solution {
    pub fn max_value(mut events: Vec<Vec<i32>>, k: i32) -> i32 {
        // 按结束时间升序排序，便于二分查找不冲突的前驱会议
        events.sort_unstable_by_key(|e| e[1]);
        let n = events.len();
        // 最多只能参加 n 个会议，若 k > n 则取 n
        let k = (k as usize).min(n);

        // dp[i][j]：考虑前 i 个会议，选择至多 j 个能获得的最大价值
        let mut dp = vec![vec![0; k + 1]; n + 1];

        for (i, e) in events.iter().enumerate() {
            let start = e[0];
            let value = e[2];
            // p 是第一个结束时间 >= start 的会议下标
            // 因此前 p 个会议的结束时间都 < start，与当前会议不冲突
            let p = events[..i].partition_point(|a| a[1] < start);

            // 前 i+1 个会议最多选 i+1 个，因此 j 的上界为 min(i+1, k)
            for j in 1..=k.min(i + 1) {
                // 不选第 i 个会议：dp[i][j]
                // 选第 i 个会议：dp[p][j - 1] + value
                dp[i + 1][j] = dp[i][j].max(dp[p][j - 1] + value);
            }

            // 确保 dp[i+1][j] 表示“至多 j 个”，即单调不减
            for j in 1..=k {
                dp[i + 1][j] = dp[i + 1][j].max(dp[i + 1][j - 1]);
            }
        }

        dp[n][k]
    }
}
```
