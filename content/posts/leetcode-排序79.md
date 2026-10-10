---
title: "leetcode-排序79"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 最大和最小 K 个元素的绝对差

给你一个整数数组 nums 和一个整数 k。

请计算以下两者的绝对差值：

数组中 k 个 最大 元素的总和；
数组中 k 个 最小 元素的总和。

返回表示此差值的整数。


```
impl Solution {
    pub fn abs_difference(mut nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;

        // 排序后，最小的 k 个在左侧，最大的 k 个在右侧
        nums.sort_unstable();

        let sum_small: i32 = nums[..k].iter().sum();
        let sum_large: i32 = nums[nums.len() - k..].iter().sum();

        // 最大 k 个之和一定不小于最小 k 个之和，差值非负
        sum_large - sum_small
    }
}
```
