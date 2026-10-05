---
title: "leetcode-二分查找30"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 在 D 天内送达包裹的能力

传送带上的包裹必须在 days 天内从一个港口运送到另一个港口。

传送带上的第 i 个包裹的重量为 weights[i]。每一天，我们都会按给出重量（weights）的顺序往传送带上装载包裹。我们装载的重量不会超过船的最大运载重量。

返回能在 days 天内将传送带上的所有包裹送达的船的最低运载能力。


```
impl Solution {
    /// 返回能在 days 天内按顺序运完所有包裹的最低船载重量。
    ///
    /// 思路：载重越大，所需天数越少，具备单调性，可二分答案。
    ///   下界 left  = max(weights)   —— 至少能装下最重的单个包裹
    ///   上界 right = sum(weights)   —— 一天全部装完
    /// 对给定载重 capacity，贪心地按顺序装货，计算所需天数，
    /// 若 <= days 则可行。
    pub fn ship_within_days(weights: Vec<i32>, days: i32) -> i32 {
        // 下界：最重的包裹；上界：所有包裹总重
        let mut lo = *weights.iter().max().unwrap();
        let mut hi = weights.iter().sum::<i32>();

        // 判断以 capacity 载重能否在 days 天内运完
        let feasible = |capacity: i32| -> bool {
            let mut days_used = 1;
            let mut load = 0;
            for &w in &weights {
                if load + w > capacity {
                    days_used += 1;
                    load = 0;
                }
                load += w;
            }
            days_used <= days
        };

        // 二分查找最小可行载重
        while lo < hi {
            let mid = lo + (hi - lo) / 2;
            if feasible(mid) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        lo
    }
}
```
