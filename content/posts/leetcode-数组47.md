---
title: "leetcode-数组47"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 超过阈值的最少操作数 I

给你一个下标从 0 开始的整数数组 nums 和一个整数 k 。

一次操作中，你可以删除 nums 中的最小元素。

你需要使数组中的所有元素都大于或等于 k ，请你返回需要的 最少 操作次数。


```
impl Solution {
    /// 返回使所有元素 ≥ k 所需的最少删除次数。
    ///
    /// 每次删除当前最小元素，因此只需统计有多少个元素 < k，
    /// 这些元素最终都必须被删除。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn min_operations(nums: Vec<i32>, k: i32) -> i32 {
        nums.iter().filter(|&&x| x < k).count() as i32
    }
}
```
