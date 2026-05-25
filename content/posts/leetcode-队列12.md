---
title: "leetcode-队列12"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 使二进制数组全部等于 1 的最少操作次数 I


给你一个二进制数组 nums 。

你可以对数组执行以下操作 任意 次（也可以 0 次）：

选择数组中 任意连续 3 个元素，并将它们 全部反转 。

反转 一个元素指的是将它的值从 0 变 1 ，或者从 1 变 0 。

请你返回将 nums 中所有元素变为 1 的 最少 操作次数。如果无法全部变成 1 ，返回 -1 。



```
impl Solution {
    pub fn min_operations(mut nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut ops = 0;

        for i in 0..n {
            if nums[i] == 0 {
                // 剩余不足3个元素时无法翻转
                if i + 2 >= n {
                    return -1;
                }
                // 翻转当前位置及后两个位置（0↔1）
                nums[i] = 1;
                nums[i + 1] ^= 1;
                nums[i + 2] ^= 1;
                ops += 1;
            }
        }
        ops
    }
}
```
