---
title: "leetcode-计数28"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 不同整数的最少数目

给你一个整数数组 arr 和一个整数 k 。现需要从数组中恰好移除 k 个元素，请找出移除后数组中不同整数的最少数目。


```
impl Solution {
    pub fn find_least_num_of_unique_ints(arr: Vec<i32>, k: i32) -> i32 {
        use std::collections::HashMap;

        // 1. 统计每个数字的出现频率
        let mut freq = HashMap::new();
        for &num in &arr {
            *freq.entry(num).or_insert(0) += 1;
        }

        // 2. 按频率升序排列，优先移除出现次数少的数字
        let mut frequencies: Vec<i32> = freq.values().copied().collect();
        frequencies.sort_unstable();

        let mut remaining = frequencies.len() as i32;
        let mut to_remove = k;

        // 3. 从频率最低的开始移除
        for &freq_count in &frequencies {
            if to_remove >= freq_count {
                to_remove -= freq_count;
                remaining -= 1;
            } else {
                break; // 不够移除当前数字的全部，停止
            }
        }

        remaining
    }
}
```
