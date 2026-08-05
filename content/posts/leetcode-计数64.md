---
title: "leetcode-计数64"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 多个数组求交集

给你一个二维整数数组 nums ，其中 nums[i] 是由 不同 正整数组成的一个非空数组，按 升序排列 返回一个数组，数组中的每个元素在 nums 所有数组 中都出现过。




```
use std::collections::HashSet;

impl Solution {
    pub fn intersection(nums: Vec<Vec<i32>>) -> Vec<i32> {
        // 从第一个数组开始，求所有数组的交集
        let mut result: HashSet<i32> = nums[0].iter().copied().collect();

        for arr in &nums[1..] {
            let set: HashSet<i32> = arr.iter().copied().collect();
            result = result.intersection(&set).copied().collect();

            // 如果交集为空，提前返回
            if result.is_empty() {
                return Vec::new();
            }
        }

        // 按升序排列
        let mut result: Vec<i32> = result.into_iter().collect();
        result.sort_unstable();
        result
    }
}
```
