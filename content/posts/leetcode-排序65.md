---
title: "leetcode-排序65"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 大于等于顺序前缀和的最小缺失整数

给你一个下标从 0 开始的整数数组 nums 。

如果一个前缀 nums[0..i] 满足对于 1 <= j <= i 的所有元素都有 nums[j] = nums[j - 1] + 1 ，那么我们称这个前缀是一个 顺序前缀 。特殊情况是，只包含 nums[0] 的前缀也是一个 顺序前缀 。

请你返回 nums 中没有出现过的 最小 整数 x ，满足 x 大于等于 最长 顺序前缀的和。




```
use std::collections::HashSet;

impl Solution {
    pub fn missing_integer(nums: Vec<i32>) -> i32 {
        // 1. 求最长顺序前缀的和
        let mut total = nums[0];
        for w in nums.windows(2) {
            if w[1] == w[0] + 1 {
                total += w[1];
            } else {
                break;
            }
        }

        // 2. 从 total 开始，找到第一个未在 nums 中出现的整数
        let num_set: HashSet<i32> = nums.into_iter().collect();
        while num_set.contains(&total) {
            total += 1;
        }

        total
    }
}
```
