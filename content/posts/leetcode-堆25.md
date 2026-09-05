---
title: "leetcode-堆25"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 数组中两元素的最大乘积

给你一个整数数组 nums。

请你选择数组的两个 不同 下标 i 和 j，

返回 (nums[i] - 1) * (nums[j] - 1) 的 最大值。


```
impl Solution {
    pub fn max_product(mut nums: Vec<i32>) -> i32 {
        let n = nums.len();
        nums.sort();
        (nums[n - 1] - 1) * (nums[n - 2] - 1)
    }
}
```
