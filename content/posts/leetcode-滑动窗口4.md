---
title: "leetcode-滑动窗口4"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 长度最小的子数组

给定一个含有 n 个正整数的数组和一个正整数 target 。

找出该数组中满足其总和大于等于 target 的长度最小的 子数组 [numsl, numsl+1, ..., numsr-1, numsr] ，并返回其长度。如果不存在符合条件的子数组，返回 0 。


```
impl Solution {
    pub fn min_sub_array_len(target: i32, nums: Vec<i32>) -> i32 {
        let mut left = 0;
        let mut sum = 0;
        let mut min_len = usize::MAX;

        for (right, &val) in nums.iter().enumerate() {
            sum += val;

            // 收缩左边界，直到子数组和小于 target
            while sum >= target {
                min_len = min_len.min(right - left + 1);
                sum -= nums[left];
                left += 1;
            }
        }

        if min_len == usize::MAX { 0 } else { min_len as i32 }
    }
}
```
