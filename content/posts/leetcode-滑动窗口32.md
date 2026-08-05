---
title: "leetcode-滑动窗口32"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 大小为 K 且平均值大于等于阈值的子数组数目

给你一个整数数组 arr 和两个整数 k 和 threshold 。

请你返回长度为 k 且平均值大于等于 threshold 的子数组数目。


```
impl Solution {
    pub fn num_of_subarrays(arr: Vec<i32>, k: i32, threshold: i32) -> i32 {
        let k = k as usize;
        let target = k as i32 * threshold;
        let mut window_sum = 0;
        let mut count = 0;

        // 先计算第一个窗口的和
        for i in 0..k {
            window_sum += arr[i];
        }
        if window_sum >= target {
            count += 1;
        }

        // 滑动窗口
        for i in k..arr.len() {
            window_sum += arr[i] - arr[i - k];
            if window_sum >= target {
                count += 1;
            }
        }

        count
    }
}
```
