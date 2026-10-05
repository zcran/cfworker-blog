---
title: "leetcode-二分查找37"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 制作 m 束花所需的最少天数

给你一个整数数组 bloomDay，以及两个整数 m 和 k 。

现需要制作 m 束花。制作花束时，需要使用花园中 相邻的 k 朵花 。

花园中有 n 朵花，第 i 朵花会在 bloomDay[i] 时盛开，恰好 可以用于 一束 花中。

请你返回从花园中摘 m 束花需要等待的 最少 的天数。如果不能摘到 m 束花则返回 -1。


```
impl Solution {
    /// 返回摘 m 束花（每束需相邻 k 朵）所需等待的最少天数，无法完成返回 -1。
    ///
    /// 思路：二分答案。
    ///   天数 d 越大，可用的花越多，能摘的花束数单调不减，具备二分性。
    ///   对给定天数 d，贪心扫描 bloomDay：连续开花的朵数达到 k 即可摘一束。
    ///
    ///   下界 lo = min(bloomDay)，上界 hi = max(bloomDay)。
    ///   若 m * k > n，直接返回 -1。
    pub fn min_days(bloom_day: Vec<i32>, m: i32, k: i32) -> i32 {
        let n = bloom_day.len();

        // 用 i64 防止 m * k 溢出
        if (m as i64) * (k as i64) > n as i64 {
            return -1;
        }

        // 二分查找最小可行天数
        let mut lo = *bloom_day.iter().min().unwrap();
        let mut hi = *bloom_day.iter().max().unwrap();

        while lo < hi {
            let mid = lo + (hi - lo) / 2;

            // 检查等待 mid 天能否摘到 m 束花
            let mut bouquets = 0;
            let mut consecutive = 0;
            let mut ok = false;

            for &day in &bloom_day {
                if day <= mid {
                    consecutive += 1;
                    if consecutive == k {
                        bouquets += 1;
                        consecutive = 0;
                        if bouquets >= m {
                            ok = true;
                            break;
                        }
                    }
                } else {
                    consecutive = 0;
                }
            }

            if ok {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }

        lo
    }
}
```
