---
title: "leetcode-滑动窗口57"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计得分小于 K 的子数组数目

一个数组的 分数 定义为数组之和 乘以 数组的长度。

比方说，[1, 2, 3, 4, 5] 的分数为 (1 + 2 + 3 + 4 + 5) * 5 = 75 。

给你一个正整数数组 nums 和一个整数 k ，请你返回 nums 中分数 严格小于 k 的 非空整数子数组数目。

子数组 是数组中的一个连续元素序列。


```
impl Solution {
    pub fn count_subarrays(nums: Vec<i32>, k: i64) -> i64 {
        let n = nums.len();
        let mut count = 0;
        let mut sum = 0;
        let mut left = 0;

        // 枚举右端点
        for right in 0..n {
            sum += nums[right] as i64;

            // 如果当前窗口分数 >= k，左移左指针直到满足条件
            while left <= right && sum * (right - left + 1) as i64 >= k {
                sum -= nums[left] as i64;
                left += 1;
            }

            // 以 right 为右端点，所有左端点在 [left, right] 的子数组都满足条件
            // 数量为 right - left + 1
            count += (right - left + 1) as i64;
        }

        count
    }
}
```
