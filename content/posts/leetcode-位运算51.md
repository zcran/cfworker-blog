---
title: "leetcode-位运算51"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 计算 K 置位下标对应元素的和

给你一个下标从 0 开始的整数数组 nums 和一个整数 k 。

请你用整数形式返回 nums 中的特定元素之 和 ，这些特定元素满足：其对应下标的二进制表示中恰存在 k 个置位。

整数的二进制表示中的 1 就是这个整数的 置位 。

例如，21 的二进制表示为 10101 ，其中有 3 个置位。


```
impl Solution {
    pub fn sum_indices_with_k_set_bits(nums: Vec<i32>, k: i32) -> i32 {
        // 只需检查下标 0..nums.len()，统计其中二进制恰好有 k 个 1 的下标并累加对应元素。
        nums.iter()
            .enumerate()
            .filter(|&(i, _)| i.count_ones() as i32 == k)
            .map(|(_, &v)| v)
            .sum()
    }
}
```
