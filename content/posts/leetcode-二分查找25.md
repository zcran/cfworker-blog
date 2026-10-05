---
title: "leetcode-二分查找25"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 爱吃香蕉的珂珂

珂珂喜欢吃香蕉。这里有 n 堆香蕉，第 i 堆中有 piles[i] 根香蕉。警卫已经离开了，将在 h 小时后回来。

珂珂可以决定她吃香蕉的速度 k （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 k 根。如果这堆香蕉少于 k 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。

珂珂喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。

返回她可以在 h 小时内吃掉所有香蕉的最小速度 k（k 为整数）。


```
impl Solution {
    /// 返回在 h 小时内吃完所有香蕉的最小整数速度 k。
    ///
    /// 思路：速度 k 越大，所需时间越少，具备单调性，可二分答案。
    /// 对给定速度 k，每堆耗时 ceil(p / k) = (p - 1) / k + 1，
    /// 总耗时 <= h 则 k 可行。
    ///
    /// 二分区间取 (left, right]，left 为不可行速度，right 为可行速度，
    /// 循环结束后 right 即为最小可行速度。
    pub fn min_eating_speed(piles: Vec<i32>, h: i32) -> i32 {
        // 判断以速度 k 能否在 h 小时内吃完
        let feasible = |k: i32| -> bool {
            let mut hours = 0i32;
            for &p in &piles {
                hours += (p - 1) / k + 1;
                if hours > h {
                    return false;
                }
            }
            true
        };

        let mut lo = 0; // 不可行（下界外）
        let mut hi = *piles.iter().max().unwrap(); // 可行（最大堆一小时吃完）

        // 二分查找最小可行速度
        while lo + 1 < hi {
            let mid = lo + (hi - lo) / 2;
            if feasible(mid) {
                hi = mid;
            } else {
                lo = mid;
            }
        }
        hi
    }
}
```
