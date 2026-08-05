---
title: "leetcode-滑动窗口72"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 找出最长等值子数组

给你一个下标从 0 开始的整数数组 nums 和一个整数 k 。

如果子数组中所有元素都相等，则认为子数组是一个 等值子数组 。注意，空数组是 等值子数组 。

从 nums 中删除最多 k 个元素后，返回可能的最长等值子数组的长度。

子数组 是数组中一个连续且可能为空的元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn longest_equal_subarray(nums: Vec<i32>, k: i32) -> i32 {
        // 记录每个数值出现的所有索引位置
        let mut positions: HashMap<i32, Vec<usize>> = HashMap::new();
        for (idx, &val) in nums.iter().enumerate() {
            positions.entry(val).or_insert_with(Vec::new).push(idx);
        }

        let k = k as i32;
        let mut max_len = 0;

        // 对每个数值的索引列表，用滑动窗口找最长连续段（允许删除 k 个其他元素）
        for indices in positions.values() {
            let mut left = 0;
            for right in 0..indices.len() {
                // 窗口 [left, right] 内需要删除的非目标元素个数：
                // 跨度 - 目标元素个数 = (indices[right] - indices[left]) - (right - left)
                while (indices[right] - indices[left]) as i32 - (right - left) as i32 > k {
                    left += 1;
                }
                max_len = max_len.max((right - left + 1) as i32);
            }
        }

        max_len
    }
}
```
