---
title: "leetcode-枚举93"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 中心子数组的数量

给你一个整数数组 nums。

如果一个 子数组 的元素之和 等于 该子数组中的 至少一个元素，则该子数组被称为 中心子数组。

返回数组 nums 中 中心子数组 的数量。

子数组 是数组中的一个连续、非空元素序列。


```
use std::collections::HashSet;

impl Solution {
    pub fn centered_subarrays(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut ans = 0;

        // 枚举每个子数组的起点
        for i in 0..n {
            let mut seen = HashSet::new();
            let mut sum = 0;

            // 从起点 i 开始扩展子数组
            for j in i..n {
                let x = nums[j];
                seen.insert(x);
                sum += x;

                // 如果子数组和等于其中某个元素，则计数
                if seen.contains(&sum) {
                    ans += 1;
                }
            }
        }

        ans
    }
}
```
