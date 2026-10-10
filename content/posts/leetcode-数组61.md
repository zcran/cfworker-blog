---
title: "leetcode-数组61"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 移除前缀使数组严格递增

给你一个整数数组 nums。

你需要从 nums 中 恰好 移除一个前缀（可以为空）。

返回一个整数，表示被移除的前缀的 最小 长度，使得剩余的数组 严格递增 。

数组的 前缀 是从数组的起始位置开始，一直延伸到任意位置的子数组。

如果一个数组的每个元素都严格大于它的前一个元素（若存在），则称该数组 严格递增 。




```
impl Solution {
    pub fn minimum_prefix_length(nums: Vec<i32>) -> i32 {
        // 从右往左找到第一个破坏严格递增的位置 i（即 nums[i-1] >= nums[i]）
        // 需移除前缀 [0, i-1]，长度为 i
        (1..nums.len())
            .rev()
            .find(|&i| nums[i - 1] >= nums[i])
            .map_or(0, |i| i as i32)
    }
}
```
