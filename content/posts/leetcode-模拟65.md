---
title: "leetcode-模拟65"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 找到和为给定整数的三个连续整数

给你一个整数 num ，请你返回三个连续的整数，它们的 和 为 num 。如果 num 无法被表示成三个连续整数的和，请你返回一个 空 数组。


```
impl Solution {
    /// 返回和为 num 的三个连续整数。
    /// 设中间数为 a，则三数为 a-1, a, a+1，和为 3a。
    /// 故 num 必须能被 3 整除，中间数 a = num / 3。
    pub fn sum_of_three(num: i64) -> Vec<i64> {
        let mid = num / 3;
        // 用乘法回验替代取模，避免负数取模歧义（如 -4 % 3 == -1）
        if 3 * mid == num {
            vec![mid - 1, mid, mid + 1]
        } else {
            Vec::new()
        }
    }
}
```
