---
title: "leetcode-计数88"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 最大频率元素计数

给你一个由 正整数 组成的数组 nums 。

返回数组 nums 中所有具有 最大 频率的元素的 总频率 。

元素的 频率 是指该元素在数组中出现的次数。




```
use std::collections::HashMap;

impl Solution {
    pub fn max_frequency_elements(nums: Vec<i32>) -> i32 {
        // 统计每个元素的出现频率
        let mut freq = HashMap::with_capacity(nums.len());
        for &x in &nums {
            *freq.entry(x).or_insert(0) += 1;
        }

        // 找到最大频率
        let max_freq = freq.values().max().copied().unwrap_or(0);

        // 计算所有具有最大频率的元素的总频率（即 最大频率 × 拥有该频率的元素个数）
        freq.values()
            .filter(|&&f| f == max_freq)
            .count() as i32
            * max_freq
    }
}
```
