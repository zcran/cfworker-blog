---
title: "leetcode-数学61"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 使所有元素都可以被 3 整除的最少操作数

给你一个整数数组 nums 。一次操作中，你可以将 nums 中的 任意 一个元素增加或者减少 1 。

请你返回将 nums 中所有元素都可以被 3 整除的 最少 操作次数。


```
impl Solution {
    pub fn minimum_operations(nums: Vec<i32>) -> i32 {
        // 对每个元素 x，只需考虑它对 3 的余数 r：
        //   r == 0 → 已是 3 的倍数，0 次操作
        //   r == 1 → 减 1（1 次）或加 2（2 次），取 min = 1
        //   r == 2 → 加 1（1 次）或减 2（2 次），取 min = 1
        // 即 min(r, 3 - r) 对所有元素求和。
        nums.iter().map(|&x| x % 3).map(|r| r.min(3 - r)).sum()
    }
}
```
