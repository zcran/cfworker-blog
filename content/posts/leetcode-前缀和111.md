---
title: "leetcode-前缀和111"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 变长子数组求和

给你一个长度为 n 的整数数组 nums 。对于 每个 下标 i（0 <= i < n），定义对应的子数组 nums[start ... i]（start = max(0, i - nums[i])）。

返回为数组中每个下标定义的子数组中所有元素的总和。

子数组 是数组中的一个连续、非空 的元素序列。



```
impl Solution {
    pub fn subarray_sum(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        // 前缀和数组，prefix[i] 表示 nums[0..i) 的和
        let mut prefix = vec![0; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] + nums[i];
        }

        let mut total = 0;
        for i in 0..n {
            // start = max(0, i - nums[i])
            let start = i.saturating_sub(nums[i] as usize);
            // 子数组和为 prefix[i+1] - prefix[start]
            total += prefix[i + 1] - prefix[start];
        }

        total
    }
}
```
