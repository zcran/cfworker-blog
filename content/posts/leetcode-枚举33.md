---
title: "leetcode-枚举33"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


##  等差三元组的数目

给你一个下标从 0 开始、严格递增 的整数数组 nums 和一个正整数 diff 。如果满足下述全部条件，则三元组 (i, j, k) 就是一个 等差三元组 ：

i < j < k ，
nums[j] - nums[i] == diff 且
nums[k] - nums[j] == diff

返回不同 等差三元组 的数目。


```
use std::collections::HashSet;

impl Solution {
    pub fn arithmetic_triplets(nums: Vec<i32>, diff: i32) -> i32 {
        let mut seen = HashSet::new();
        let mut count = 0;

        for &num in &nums {
            // 检查当前数字能否作为等差三元组的最后一个元素
            // 需要存在 num - diff 和 num - 2*diff
            if seen.contains(&(num - diff)) && seen.contains(&(num - 2 * diff)) {
                count += 1;
            }
            seen.insert(num);
        }

        count
    }
}
```
