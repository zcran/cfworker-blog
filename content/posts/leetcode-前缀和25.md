---
title: "leetcode-前缀和25"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 一维数组的动态和

给你一个数组 nums 。数组「动态和」的计算公式为：runningSum[i] = sum(nums[0]…nums[i]) 。

请返回 nums 的动态和。


```
impl Solution {
    pub fn running_sum(mut nums: Vec<i32>) -> Vec<i32> {
        // 原地累加，将每个位置变为前缀和
        for i in 1..nums.len() {
            nums[i] += nums[i - 1];
        }
        nums
    }
}
```
