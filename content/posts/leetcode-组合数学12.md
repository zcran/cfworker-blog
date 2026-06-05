---
title: "leetcode-组合数学12"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


##  找出所有子集的异或总和再求和


一个数组的 异或总和 定义为数组中所有元素按位 XOR 的结果；如果数组为 空 ，则异或总和为 0 。

例如，数组 [2,5,6] 的 异或总和 为 2 XOR 5 XOR 6 = 1 。
给你一个数组 nums ，请你求出 nums 中每个 子集 的 异或总和 ，计算并返回这些值相加之 和 。

注意：在本题中，元素 相同 的不同子集应 多次 计数。

数组 a 是数组 b 的一个 子集 的前提条件是：从 b 删除几个（也可能不删除）元素能够得到 a 。


```
impl Solution {
    pub fn subset_xor_sum(nums: Vec<i32>) -> i32 {
        // 计算所有数的按位或
        let or_all = nums.iter().fold(0, |acc, &x| acc | x);
        // 2^(n-1) 的快速幂，注意 n=0 时无意义（题目保证 n>=1）
        let mul = 1 << (nums.len() - 1);
        or_all * mul
    }
}
```
