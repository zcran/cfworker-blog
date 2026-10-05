---
title: "leetcode-贪心142"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 使二进制数组全部等于 1 的最少操作次数 II

给你一个二进制数组 nums 。

你可以对数组执行以下操作 任意 次（也可以 0 次）：

选择数组中 任意 一个下标 i ，并将从下标 i 开始一直到数组末尾 所有 元素 反转 。
反转 一个元素指的是将它的值从 0 变 1 ，或者从 1 变 0 。

请你返回将 nums 中所有元素变为 1 的 最少 操作次数。


```
impl Solution {
    pub fn min_operations(nums: Vec<i32>) -> i32 {
        // 从左到右扫描，用 flip 记录当前已累积的反转次数（奇偶性）。
        // 当前元素的实际值 = num ^ (flip & 1)。
        // 若实际值为 0，则需要从该位置再做一次后缀反转，flip 加一。
        let mut flips = 0;
        for num in nums {
            if num == flips & 1 {
                flips += 1;
            }
        }
        flips
    }
}
```
