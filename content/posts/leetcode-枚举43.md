---
title: "leetcode-枚举43"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 特殊元素平方和

给你一个下标从 1 开始、长度为 n 的整数数组 nums 。

对 nums 中的元素 nums[i] 而言，如果 n 能够被 i 整除，即 n % i == 0 ，则认为 nums[i] 是一个 特殊元素 。

返回 nums 中所有 特殊元素 的 平方和 。


```
impl Solution {
    pub fn sum_of_squares(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        // 枚举所有 n 的因数（即特殊索引）
        // 只有这些位置的元素才需要计算平方
        let mut sum = 0;
        for i in 1..=n {
            if n % i == 0 {
                let val = nums[i - 1];
                sum += val * val;
            }
        }
        sum
    }
}
```
