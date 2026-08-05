---
title: "leetcode-滑动窗口13"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 子数组最大平均数 I

给你一个由 n 个元素组成的整数数组 nums 和一个整数 k 。

请你找出平均数最大且 长度为 k 的连续子数组，并输出该最大平均数。

任何误差小于 10-5 的答案都将被视为正确答案。


```
impl Solution {
    pub fn find_max_average(nums: Vec<i32>, k: i32) -> f64 {
        let k = k as usize;
        let mut window_sum: i32 = nums.iter().take(k).sum();
        let mut max_sum = window_sum;

        // 滑动窗口：每次右移一位，更新窗口和
        for i in k..nums.len() {
            window_sum += nums[i] - nums[i - k];
            max_sum = max_sum.max(window_sum);
        }

        max_sum as f64 / k as f64
    }
}
```
