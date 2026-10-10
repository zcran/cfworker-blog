---
title: "leetcode-排序12"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 三个数的最大乘积

给你一个整型数组 nums。

在数组中找出由三个数组成的 最大 乘积，并返回这个 最大 乘积。


```
impl Solution {
    /// 求数组中三个数组成的最大乘积
    ///
    /// 思路：排序后最大乘积只可能是两种情况之一：
    ///   1. 最大的三个数相乘（全正 / 全负中最接近零的情形）；
    ///   2. 最小的两个负数与最大的正数相乘（两负一正得正且最大）。
    /// 时间 O(n log n)，额外空间 O(1)。
    pub fn maximum_product(mut nums: Vec<i32>) -> i32 {
        nums.sort_unstable();
        let n = nums.len();

        let top3 = nums[n - 3] * nums[n - 2] * nums[n - 1];
        let two_neg_one_pos = nums[0] * nums[1] * nums[n - 1];

        top3.max(two_neg_one_pos)
    }
}
```
