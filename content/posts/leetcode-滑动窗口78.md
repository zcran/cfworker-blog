---
title: "leetcode-滑动窗口78"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最多 K 个重复元素的最长子数组

给你一个整数数组 nums 和一个整数 k 。

一个元素 x 在数组中的 频率 指的是它在数组中的出现次数。

如果一个数组中所有元素的频率都 小于等于 k ，那么我们称这个数组是 好 数组。

请你返回 nums 中 最长好 子数组的长度。

子数组 指的是一个数组中一段连续非空的元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn max_subarray_length(nums: Vec<i32>, k: i32) -> i32 {
        let mut freq = HashMap::new();
        let mut left = 0;
        let mut max_len = 0;

        for right in 0..nums.len() {
            let val = nums[right];
            *freq.entry(val).or_insert(0) += 1;

            // 当当前元素的频率超过 k 时，移动左边界
            while freq[&val] > k {
                let left_val = nums[left];
                *freq.get_mut(&left_val).unwrap() -= 1;
                if freq[&left_val] == 0 {
                    freq.remove(&left_val);
                }
                left += 1;
            }

            max_len = max_len.max(right - left + 1);
        }

        max_len as i32
    }
}
```
