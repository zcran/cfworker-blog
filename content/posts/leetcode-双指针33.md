---
title: "leetcode-双指针33"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 数组中的 k-diff 数对

给你一个整数数组 nums 和一个整数 k，请你在数组中找出 不同的 k-diff 数对，并返回不同的 k-diff 数对 的数目。

k-diff 数对定义为一个整数对 (nums[i], nums[j]) ，并满足下述全部条件：

· 0 <= i, j < nums.length
· i != j
· |nums[i] - nums[j]| == k

注意，|val| 表示 val 的绝对值。


```
use std::collections::HashMap;

impl Solution {
    pub fn find_pairs(nums: Vec<i32>, k: i32) -> i32 {
        // 统计每个数字出现的次数
        let mut count = HashMap::with_capacity(nums.len());
        for num in nums {
            *count.entry(num).or_insert(0) += 1;
        }

        let mut pairs = 0;
        for (&num, &freq) in &count {
            if k == 0 {
                // k=0 时，需要相同数字出现至少两次
                if freq >= 2 {
                    pairs += 1;
                }
            } else {
                // k>0 时，检查 num+k 是否存在（只检查单向避免重复计数）
                if count.contains_key(&(num + k)) {
                    pairs += 1;
                }
            }
        }

        pairs
    }
}
```
