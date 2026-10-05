---
title: "leetcode-位运算37"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 操作后的最大异或和

给你一个下标从 0 开始的整数数组 nums 。一次操作中，选择 任意 非负整数 x 和一个下标 i ，更新 nums[i] 为 nums[i] AND (nums[i] XOR x) 。

注意，AND 是逐位与运算，XOR 是逐位异或运算。

请你执行 任意次 更新操作，并返回 nums 中所有元素 最大 逐位异或和。


```
impl Solution {
    /// 返回经过任意次操作后，nums 所有元素的最大逐位异或和。
    ///
    /// 对任意数字，`nums[i] AND (nums[i] XOR x)` 的结果一定是 `nums[i]` 的子集
    /// （即只可能把某些 1 变成 0）。而对某一位，只要 nums 中任一元素该位为 1，
    /// 就能通过操作让最终异或和该位也为 1。因此最大值等于所有元素的按位或。
    pub fn maximum_xor(nums: Vec<i32>) -> i32 {
        nums.into_iter().fold(0, |acc, x| acc | x)
    }
}
```
