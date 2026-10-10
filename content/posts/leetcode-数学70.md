---
title: "leetcode-数学70"
date: 2026-10-07T20:39:33+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 使数组和能被 K 整除的最少操作次数

给你一个整数数组 nums 和一个整数 k。你可以执行以下操作任意次：

选择一个下标 i，并将 nums[i] 替换为 nums[i] - 1。

返回使数组元素之和能被 k 整除所需的最小操作次数。


```
impl Solution {
    pub fn min_operations(nums: Vec<i32>, k: i32) -> i32 {
        // 只需将总和减去 (sum % k)，即可让总和被 k 整除，
        // 每次操作使总和减 1，故最小操作次数 = sum % k。
        let sum: i32 = nums.iter().sum();
        sum % k
    }
}

```
