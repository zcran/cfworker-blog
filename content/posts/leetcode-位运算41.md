---
title: "leetcode-位运算41"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 查询数组异或美丽值

给你一个下标从 0 开始的整数数组 nums 。

三个下标 i ，j 和 k 的 有效值 定义为 ((nums[i] | nums[j]) & nums[k]) 。

一个数组的 异或美丽值 是数组中所有满足 0 <= i, j, k < n  的三元组 (i, j, k) 的 有效值 的异或结果。

请你返回 nums 的异或美丽值。

注意：

val1 | val2 是 val1 和 val2 的按位或。
val1 & val2 是 val1 和 val2 的按位与。


```
impl Solution {
    pub fn xor_beauty(nums: Vec<i32>) -> i32 {
        // 异或美丽值恰好等于数组中所有元素的异或和。
        // 推导：所有三元组 (i, j, k) 的有效值 ((nums[i] | nums[j]) & nums[k]) 异或后，
        // 每一位的贡献只取决于 nums 中该位为 1 的元素个数，最终等价于 nums 的异或和。
        nums.into_iter().fold(0, |acc, x| acc ^ x)
    }
}
```
