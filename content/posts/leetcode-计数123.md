---
title: "leetcode-计数123"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 频率唯一的第一个元素

给你一个整数数组 nums。

返回数组中第一个（从左到右扫描）出现频率与众不同 的元素。如果不存在这样的元素，返回 -1。


```
use std::collections::HashMap;

impl Solution {
    /// 返回数组中第一个频率"独一无二"的元素。
    ///
    /// 思路：
    /// 1. 统计每个数字的出现频率
    /// 2. 统计每个频率值被多少个数字共享
    /// 3. 按原顺序扫描，找到第一个频率只出现一次的元素
    pub fn first_unique_freq(nums: Vec<i32>) -> i32 {
        // 步骤1：统计每个数字的频率
        // 使用 with_capacity 避免扩容，提升性能
        let n = nums.len();
        let mut freq = HashMap::with_capacity(n);
        for &x in &nums {
            *freq.entry(x).or_insert(0) += 1;
        }

        // 步骤2：统计每个频率值的出现次数
        // 频率最大不会超过 n，用 Vec 替代 HashMap 更省内存、更快
        let mut freq_count = vec![0u32; n + 1];
        for &count in freq.values() {
            freq_count[count] += 1;
        }

        // 步骤3：按原顺序找到第一个"独一无二"的频率对应的元素
        for &x in &nums {
            if freq_count[freq[&x] as usize] == 1 {
                return x;
            }
        }

        -1
    }
}
```
