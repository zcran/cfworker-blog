---
title: "leetcode-二分查找35"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 使结果不超过阈值的最小除数

给你一个整数数组 nums 和一个正整数 threshold  ，你需要选择一个正整数作为除数，然后将数组里每个数都除以它，并对除法结果求和。

请你找出能够使上述结果小于等于阈值 threshold 的除数中 最小 的那个。

每个数除以除数后都向上取整，比方说 7/3 = 3 ， 10/2 = 5 。

题目保证一定有解。


```
impl Solution {
    /// 返回使「向上取整除法之和 ≤ threshold」的最小正整数除数。
    ///
    /// 思路：除数越大，总和越小，具备单调性，可二分答案。
    ///   下界 lo = 1，上界 hi = max(nums)（此时每个数除以它都不超过 1，和最小）。
    ///   对给定除数 m，总和为 sum(ceil(x / m)) = sum((x + m - 1) / m)，
    ///   若总和 ≤ threshold 则 m 可行，尝试更小；否则需更大。
    pub fn smallest_divisor(nums: Vec<i32>, threshold: i32) -> i32 {
        // 判断除数 m 是否满足总和 ≤ threshold（提前退出加速）
        let feasible = |m: i32| -> bool {
            let mut sum = 0;
            for &x in &nums {
                sum += (x + m - 1) / m;
                if sum > threshold {
                    return false;
                }
            }
            true
        };

        let mut lo = 0; // 不可行（下界外）
        let mut hi = *nums.iter().max().unwrap(); // 必然可行

        // 二分查找最小可行除数
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
