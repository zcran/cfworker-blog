---
title: "leetcode-二分查找85"
date: 2026-09-28T12:29:16+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 减小数组使其满足条件的最小 K 值

给你一个 正 整数数组 nums。

对于一个正整数 k，定义 nonPositive(nums, k) 为使 nums 的每个元素都变为 非正数 所需的 最小 操作 次数。在一次操作中，你可以选择一个下标 i 并将 nums[i] 减少 k。

返回一个整数，表示满足 nonPositive(nums, k) <= k2 的 k 的 最小 值。


```
impl Solution {
    pub fn minimum_k(nums: Vec<i32>) -> i32 {
        let n = nums.len() as f64;
        let sum: i64 = nums.iter().map(|&x| x as i64).sum();

        // 下界估计：ceil(sqrt(n)) 与 ceil(cbrt(sum)) 的较大者
        let mut k = (n.sqrt().ceil() as i64).max((sum as f64).cbrt().ceil() as i64);

        loop {
            // 计算将所有数变为非正数所需的操作次数
            let ops: i64 = nums.iter().map(|&x| (x as i64 + k - 1) / k).sum();
            if ops <= k * k {
                return k as i32;
            }
            k += 1;
        }
    }
}
```
