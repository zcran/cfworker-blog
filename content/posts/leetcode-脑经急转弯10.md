---
title: "leetcode-脑经急转弯10"
date: 2026-03-24T20:55:57+08:00
tags: ["leetcode", "脑筋急转弯"]
draft: false
---


## 所有数对的异或和

给你两个下标从 0 开始的数组 nums1 和 nums2 ，两个数组都只包含非负整数。请你求出另外一个数组 nums3 ，包含 nums1 和 nums2 中 所有数对 的异或和（nums1 中每个整数都跟 nums2 中每个整数 恰好 匹配一次）。

请你返回 nums3 中所有整数的 异或和 。

```
impl Solution {
    pub fn xor_all_nums(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let nums1_xor = if nums2.len() % 2 == 1 {
            nums1.iter().fold(0, |acc, x| acc ^ x)
        } else {
            0
        };
        let nums2_xor = if nums1.len() % 2 == 1 {
            nums2.iter().fold(0, |acc, x| acc ^ x)
        } else {
            0
        };
        nums1_xor ^ nums2_xor
    }
}

```
