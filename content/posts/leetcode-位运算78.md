---
title: "leetcode-位运算78"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 按位异或非零的最长子序列

给你一个整数数组 nums。

返回 nums 中 按位异或（XOR）计算结果 非零 的 最长子序列 的长度。如果不存在这样的 子序列 ，返回 0 。

子序列 是一个 非空 数组，可以通过从原数组中删除一些或不删除任何元素（不改变剩余元素的顺序）派生而来。


```
impl Solution {
    pub fn longest_subsequence(nums: Vec<i32>) -> i32 {
        // 思路：
        // 1. 若整个数组的 XOR 非零，则取全部元素即为答案，长度为 n。
        // 2. 若整个数组 XOR 为零：
        //    a. 若全为 0，任何子序列 XOR 都是 0，无解，返回 0。
        //    b. 否则存在非零元素，去掉任意一个非零元素后剩余部分 XOR 非零，
        //       长度为 n - 1（这是能达到的最大值，因为 n 长度时 XOR 为零）。
        let total_xor = nums.iter().fold(0, |acc, &x| acc ^ x);

        if total_xor != 0 {
            nums.len() as i32
        } else if nums.iter().all(|&x| x == 0) {
            0
        } else {
            nums.len() as i32 - 1
        }
    }
}
```
