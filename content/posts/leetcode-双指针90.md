---
title: "leetcode-双指针90"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 与对应负数同时存在的最大正整数

给你一个 不包含 任何零的整数数组 nums ，找出自身与对应的负数都在数组中存在的最大正整数 k 。

返回正整数 k ，如果不存在这样的整数，返回 -1 。


```
use std::collections::HashSet;

impl Solution {
    pub fn find_max_k(nums: Vec<i32>) -> i32 {
        let mut seen = HashSet::new();
        let mut result = -1;

        for num in nums {
            // 若其相反数已出现，说明 num 与其负数同时存在
            if seen.contains(&-num) {
                result = result.max(num.abs());
            }
            seen.insert(num);
        }

        result
    }
}
```
