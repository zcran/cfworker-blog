---
title: "leetcode-滑动窗口73"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 几乎唯一子数组的最大和

给你一个整数数组 nums 和两个正整数 m 和 k 。

请你返回 nums 中长度为 k 的 几乎唯一 子数组的 最大和 ，如果不存在几乎唯一子数组，请你返回 0 。

如果 nums 的一个子数组有至少 m 个互不相同的元素，我们称它是 几乎唯一 子数组。

子数组指的是一个数组中一段连续 非空 的元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn max_sum(nums: Vec<i32>, m: i32, k: i32) -> i64 {
        let m = m as usize;
        let k = k as usize;
        let mut max_sum = 0i64;
        let mut window_sum = 0i64;
        let mut freq = HashMap::with_capacity(k);

        for right in 0..nums.len() {
            // 加入右边界元素
            let val = nums[right];
            window_sum += val as i64;
            *freq.entry(val).or_insert(0) += 1;

            // 窗口未满，继续扩展
            if right < k - 1 {
                continue;
            }

            // 窗口已满，检查是否满足条件
            if freq.len() >= m {
                max_sum = max_sum.max(window_sum);
            }

            // 移除左边界元素
            let left_val = nums[right - k + 1];
            window_sum -= left_val as i64;
            if let Some(count) = freq.get_mut(&left_val) {
                *count -= 1;
                if *count == 0 {
                    freq.remove(&left_val);
                }
            }
        }

        max_sum
    }
}
```
