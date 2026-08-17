---
title: "leetcode-计数126"
date: 2026-08-01T10:40:58+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 找到第一个唯一偶数

给你一个整数数组 nums。

请你返回一个整数，表示 nums 中出现 恰好 一次的第一个 偶数（以数组下标最早为准）。如果不存在这样的整数，返回 -1。

如果一个整数 x 能被 2 整除，那么它就被认为是 偶数。


```
impl Solution {
    /// 返回 nums 中第一个出现恰好一次的偶数（按原数组顺序）。
    /// 若不存在，返回 -1。
    pub fn first_unique_even(nums: Vec<i32>) -> i32 {
        use std::collections::HashMap;

        // 只统计偶数的出现频率
        let mut freq = HashMap::new();
        for &x in &nums {
            if x & 1 == 0 {
                *freq.entry(x).or_insert(0) += 1;
            }
        }

        // 按原顺序找到第一个频率为 1 的偶数
        for &x in &nums {
            if x & 1 == 0 && freq.get(&x) == Some(&1) {
                return x;
            }
        }

        -1
    }
}
```
