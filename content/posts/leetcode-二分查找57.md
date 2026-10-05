---
title: "leetcode-二分查找57"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 每个小孩最多能分到多少糖果

给你一个 下标从 0 开始 的整数数组 candies 。数组中的每个元素表示大小为 candies[i] 的一堆糖果。你可以将每堆糖果分成任意数量的 子堆 ，但 无法 再将两堆合并到一起。

另给你一个整数 k 。你需要将这些糖果分配给 k 个小孩，使每个小孩分到 相同 数量的糖果。每个小孩可以拿走 至多一堆 糖果，有些糖果可能会不被分配。

返回每个小孩可以拿走的 最大糖果数目 。




```
impl Solution {
    pub fn maximum_candies(candies: Vec<i32>, k: i64) -> i32 {
        // 每个小孩最多拿到的糖果数不会超过最大的一堆，也不会超过 total / k
        let total: i64 = candies.iter().map(|&c| c as i64).sum();
        let max_pile = *candies.iter().max().unwrap() as i64;
        let upper = (total / k).min(max_pile); // 用 i64 计算，避免溢出
        if upper == 0 {
            return 0;
        }

        let mut lo = 1i64;
        let mut hi = upper;

        // 二分查找最大的可行糖果数
        while lo < hi {
            let mid = lo + (hi - lo + 1) / 2; // 上中位数

            // 判断每个小孩分 mid 颗糖时，能否分出至少 k 堆
            let mut piles = 0i64;
            for &c in &candies {
                piles += c as i64 / mid;
                if piles >= k {
                    break;
                }
            }

            if piles >= k {
                lo = mid;
            } else {
                hi = mid - 1;
            }
        }

        lo as i32
    }
}
```
