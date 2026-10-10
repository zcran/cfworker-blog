---
title: "leetcode-动态规划96"
date: 2026-10-05T20:40:00+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 访问完所有房间的第一天

你需要访问 n 个房间，房间从 0 到 n - 1 编号。同时，每一天都有一个日期编号，从 0 开始，依天数递增。你每天都会访问一个房间。

最开始的第 0 天，你访问 0 号房间。给你一个长度为 n 且 下标从 0 开始 的数组 nextVisit 。在接下来的几天中，你访问房间的 次序 将根据下面的 规则 决定：

假设某一天，你访问 i 号房间。

如果算上本次访问，访问 i 号房间的次数为 奇数 ，那么 第二天 需要访问 nextVisit[i] 所指定的房间，其中 0 <= nextVisit[i] <= i 。

如果算上本次访问，访问 i 号房间的次数为 偶数 ，那么 第二天 需要访问 (i + 1) mod n 号房间。

请返回你访问完所有房间的第一天的日期编号。题目数据保证总是存在这样的一天。由于答案可能很大，返回对 10^9 + 7 取余后的结果。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn first_day_been_in_all_rooms(next_visit: Vec<i32>) -> i32 {
        let n = next_visit.len();
        // dp[i]: 第一次访问完房间 i 并准备前往 i+1 时的天数
        // 由于 next_visit[i] <= i，dp 可以一边计算一边使用
        let mut dp = vec![0i64; n];
        dp[0] = 2; // 第 0 天访问 0 号房，第 1 天再访 0 号房，第 2 天到 1 号房

        for i in 1..n {
            let to = next_visit[i] as usize;
            // 从第一次到达 i 到再次回到 i，需要先跳到 to，再重新走到 i
            // 天数 = 2（访问 i 两次）+ 从 dp[i-1] 回退到 dp[to-1] 的差值
            let mut cur = 2 + dp[i - 1];
            if to != 0 {
                cur = (cur - dp[to - 1] + MOD) % MOD;
            }
            dp[i] = (cur + dp[i - 1]) % MOD;
        }

        // 访问完所有房间的第一天，即准备离开最后一个房间的前一天
        dp[n - 2] as i32
    }
}
```
