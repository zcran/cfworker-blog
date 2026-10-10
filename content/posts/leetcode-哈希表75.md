---
title: "leetcode-哈希表75"
date: 2026-10-09T20:08:00+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 使数组元素互不相同所需的最少操作次数

给你一个整数数组 nums，你需要确保数组中的元素 互不相同 。为此，你可以执行以下操作任意次：

从数组的开头移除 3 个元素。如果数组中元素少于 3 个，则移除所有剩余元素。

注意：空数组也视作为数组元素互不相同。返回使数组元素互不相同所需的 最少操作次数 。


```
use std::collections::HashSet;

impl Solution {
    pub fn minimum_operations(nums: Vec<i32>) -> i32 {
        // 从后往前扫描，找到最短的、元素互不相同的后缀
        // 该后缀的起始位置决定需要从前面移除多少元素
        let mut seen = HashSet::new();
        let mut i = nums.len();

        while i > 0 && !seen.contains(&nums[i - 1]) {
            seen.insert(nums[i - 1]);
            i -= 1;
        }

        // nums[0..i] 需要被移除，每次移除 3 个（向上取整）
        ((i + 2) / 3) as i32
    }
}
```
