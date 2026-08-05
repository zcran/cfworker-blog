---
title: "leetcode-滑动窗口113"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 不同元素和至少为 K 的最短子数组长度

给你一个整数数组 nums 和一个整数 k。

返回一个 子数组 的 最小 长度，使得该子数组中出现的 不同 值之和（每个值只计算一次）至少 为 k。如果不存在这样的子数组，则返回 -1。

子数组 是数组中一个连续的 非空 元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn min_length(nums: Vec<i32>, k: i32) -> i32 {
        let mut freq = HashMap::new();
        let mut sum = 0i64;
        let mut left = 0;
        let mut min_len = i32::MAX;

        for (right, &x) in nums.iter().enumerate() {
            // 1. 扩展右边界：加入 nums[right]
            let count = freq.entry(x).or_insert(0);
            *count += 1;
            if *count == 1 {
                sum += x as i64;
            }

            // 2. 收缩左边界：当窗口满足条件时，尝试找到更小的窗口
            while sum >= k as i64 {
                min_len = min_len.min((right - left + 1) as i32);

                // 移除 nums[left]
                let out = nums[left];
                let out_count = freq.get_mut(&out).unwrap();
                *out_count -= 1;
                if *out_count == 0 {
                    sum -= out as i64;
                }
                left += 1;
            }
        }

        if min_len == i32::MAX { -1 } else { min_len }
    }
}
```
