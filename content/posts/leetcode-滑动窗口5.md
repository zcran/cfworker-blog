---
title: "leetcode-滑动窗口5"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 存在重复元素 II

给你一个整数数组 nums 和一个整数 k ，判断数组中是否存在两个 不同的索引 i 和 j ，满足 nums[i] == nums[j] 且 abs(i - j) <= k 。如果存在，返回 true ；否则，返回 false 。


```
use std::collections::HashMap;

impl Solution {
    pub fn contains_nearby_duplicate(nums: Vec<i32>, k: i32) -> bool {
        // 记录每个数字最近一次出现的索引
        let mut last_seen = HashMap::with_capacity(nums.len());
        let k = k as usize;

        for (i, &num) in nums.iter().enumerate() {
            if let Some(&prev) = last_seen.get(&num) {
                if i - prev <= k {
                    return true;
                }
            }
            last_seen.insert(num, i);
        }

        false
    }
}
```
