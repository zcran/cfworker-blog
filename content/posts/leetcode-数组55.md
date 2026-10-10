---
title: "leetcode-数组55"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 循环数组中相邻元素的最大差值

给你一个 循环 数组 nums ，请你找出相邻元素之间的 最大 绝对差值。

注意：一个循环数组中，第一个元素和最后一个元素是相邻的。


```
impl Solution {
    pub fn max_adjacent_distance(nums: Vec<i32>) -> i32 {
        // 循环数组：相邻元素包括 (nums[i], nums[i+1]) 以及首尾 (nums[n-1], nums[0])
        nums.windows(2)
            .map(|w| (w[0] - w[1]).abs())
            .chain(std::iter::once((nums[0] - nums[nums.len() - 1]).abs()))
            .max()
            .unwrap()
    }
}
```
