---
title: "leetcode-二分查找34"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 规划兼职工作

你打算利用空闲时间来做兼职工作赚些零花钱。

这里有 n 份兼职工作，每份工作预计从 startTime[i] 开始到 endTime[i] 结束，报酬为 profit[i]。

给你一份兼职工作表，包含开始时间 startTime，结束时间 endTime 和预计报酬 profit 三个数组，请你计算并返回可以获得的最大报酬。

注意，时间上出现重叠的 2 份工作不能同时进行。

如果你选择的工作在时间 X 结束，那么你可以立刻进行在时间 X 开始的下一份工作。


```
impl Solution {
    /// 返回可获得的最大报酬。
    ///
    /// 思路：动态规划 + 二分。
    ///   1. 将所有工作按结束时间升序排序，便于 DP 和无重叠转移。
    ///   2. 设 dp[i] 表示考虑前 i 份工作（排序后）能获得的最大报酬。
    ///      对第 i 份工作（1-based），要么不做：dp[i] = dp[i-1]；
    ///      要么做：需要找到结束时间 <= 该工作开始时间的最后一份工作位置 j，
    ///      则 dp[i] = max(dp[i], dp[j] + profit[i-1])。
    ///   3. j 可通过二分（partition_point）快速求得。
    pub fn job_scheduling(start_time: Vec<i32>, end_time: Vec<i32>, profit: Vec<i32>) -> i32 {
        let n = start_time.len();

        // 将工作按结束时间升序排序，每项为 (start, end, profit)
        let mut jobs: Vec<(i32, i32, i32)> = (0..n)
            .map(|i| (start_time[i], end_time[i], profit[i]))
            .collect();
        jobs.sort_unstable_by_key(|&(_, e, _)| e);

        // dp[i] 表示考虑前 i 份工作能获得的最大报酬
        let mut dp = vec![0; n + 1];

        for i in 1..=n {
            let (st, _, p) = jobs[i - 1];

            // 找到结束时间 <= st 的工作数量 j，对应 dp 下标 j
            let j = jobs[..i - 1].partition_point(|&(_, e, _)| e <= st);

            // 不做当前工作 vs 做当前工作
            dp[i] = dp[i - 1].max(dp[j] + p);
        }

        dp[n]
    }
}
```
