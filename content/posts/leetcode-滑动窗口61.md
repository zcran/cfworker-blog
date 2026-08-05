---
title: "leetcode-滑动窗口61"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 长度为 K 子数组中的最大和

给你一个整数数组 nums 和一个整数 k 。请你从 nums 中满足下述条件的全部子数组中找出最大子数组和：

子数组的长度是 k，且

子数组中的所有元素 各不相同 。

返回满足题面要求的最大子数组和。如果不存在子数组满足这些条件，返回 0 。

子数组 是数组中一段连续非空的元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn maximum_subarray_sum(nums: Vec<i32>, k: i32) -> i64 {
        let k = k as usize;
        let n = nums.len();

        // 如果数组长度小于k，无法形成长度为k的子数组
        if n < k {
            return 0;
        }

        let mut window_sum: i64 = 0;
        let mut freq = HashMap::with_capacity(k);
        let mut max_sum = 0;

        // 初始化第一个窗口
        for i in 0..k {
            window_sum += nums[i] as i64;
            *freq.entry(nums[i]).or_insert(0) += 1;
        }

        // 检查第一个窗口
        if freq.len() == k {
            max_sum = window_sum;
        }

        // 滑动窗口
        for i in k..n {
            // 加入新元素
            let new_val = nums[i];
            window_sum += new_val as i64;
            *freq.entry(new_val).or_insert(0) += 1;

            // 移除旧元素
            let old_val = nums[i - k];
            window_sum -= old_val as i64;
            let count = freq.get_mut(&old_val).unwrap();
            *count -= 1;
            if *count == 0 {
                freq.remove(&old_val);
            }

            // 检查当前窗口是否所有元素都不同
            if freq.len() == k {
                max_sum = max_sum.max(window_sum);
            }
        }

        max_sum
    }
}
```
