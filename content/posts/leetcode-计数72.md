---
title: "leetcode-计数72"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 统计坏数对的数目

给你一个下标从 0 开始的整数数组 nums 。如果 i < j 且 j - i != nums[j] - nums[i] ，那么我们称 (i, j) 是一个 坏数对 。

请你返回 nums 中 坏数对 的总数目。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_bad_pairs(nums: Vec<i32>) -> i64 {
        // 好数对满足: j - i == nums[j] - nums[i]
        // 等价于: nums[i] - i == nums[j] - j
        // 统计每个差值出现的次数，好数对 = C(count, 2)
        // 坏数对 = 总数对 - 好数对
        let mut freq = HashMap::with_capacity(nums.len());
        let mut good_pairs = 0i64;

        for (i, &x) in nums.iter().enumerate() {
            let key = x - i as i32;
            let count = freq.get(&key).copied().unwrap_or(0);
            good_pairs += count as i64;  // 当前元素与之前所有同差值的元素组成好数对
            freq.insert(key, count + 1);
        }

        let total_pairs = nums.len() as i64 * (nums.len() as i64 - 1) / 2;
        total_pairs - good_pairs
    }
}
```
