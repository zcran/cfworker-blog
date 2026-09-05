---
title: "leetcode-堆17"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 数组大小减半

给你一个整数数组 arr。你可以从中选出一个整数集合，并删除这些整数在数组中的每次出现。

返回 至少 能删除数组中的一半整数的整数集合的最小大小。


```
use std::cmp::Reverse;
use std::collections::HashMap;

impl Solution {
    /// 贪心：优先删除出现频率最高的数，直到删除数量过半
    pub fn min_set_size(arr: Vec<i32>) -> i32 {
        // 统计每个数的出现频率
        let mut freq = HashMap::new();
        for &x in &arr {
            *freq.entry(x).or_insert(0) += 1;
        }

        // 按频率降序排列
        let mut counts: Vec<i32> = freq.into_values().collect();
        counts.sort_unstable_by_key(|&c| Reverse(c));

        // 从高频率开始累加，直到覆盖至少一半元素
        let half = arr.len() as i32;
        let mut removed = 0;
        for (i, &c) in counts.iter().enumerate() {
            removed += c;
            if removed * 2 >= half {
                return (i + 1) as i32;
            }
        }

        counts.len() as i32
    }
}
```
