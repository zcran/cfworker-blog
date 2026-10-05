---
title: "leetcode-位运算55"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 找出数组中的 K-or 值

给你一个整数数组 nums 和一个整数 k 。让我们通过扩展标准的按位或来介绍 K-or 操作。在 K-or 操作中，如果在 nums 中，至少存在 k 个元素的第 i 位值为 1 ，那么 K-or 中的第 i 位的值是 1 。

返回 nums 的 K-or 值。


```
impl Solution {
    pub fn find_k_or(nums: Vec<i32>, k: i32) -> i32 {
        // 对每一位统计 nums 中该位为 1 的元素个数，达到 k 个则结果该位为 1。
        let mut ans = 0;
        for i in 0..31 {
            let count = nums.iter().filter(|&&x| (x >> i) & 1 == 1).count() as i32;
            if count >= k {
                ans |= 1 << i;
            }
        }
        ans
    }
}
```
