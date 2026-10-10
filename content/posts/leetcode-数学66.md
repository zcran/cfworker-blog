---
title: "leetcode-数学66"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 数字小镇中的捣蛋鬼

数字小镇 Digitville 中，存在一个数字列表 nums，其中包含从 0 到 n - 1 的整数。每个数字本应 只出现一次，然而，有 两个 顽皮的数字额外多出现了一次，使得列表变得比正常情况下更长。

为了恢复 Digitville 的和平，作为小镇中的名侦探，请你找出这两个顽皮的数字。

返回一个长度为 2 的数组，包含这两个数字（顺序任意）。


```
impl Solution {
    pub fn get_sneaky_numbers(nums: Vec<i32>) -> Vec<i32> {
        // 利用"和"与"平方和"列方程：
        //   设多出的两个数为 a, b，n 为原范围 0..n-1 的上界
        //   sum  - n(n-1)/2           = a + b
        //   sqsum - n(n-1)(2n-1)/6    = a² + b²
        // 联立解得 a、b。
        let n = nums.len() as i64 - 2;

        let sum: i64 = nums.iter().map(|&x| x as i64).sum();
        let sqsum: i64 = nums.iter().map(|&x| x as i64 * x as i64).sum();

        let s = sum - n * (n - 1) / 2;                       // a + b
        let q = sqsum - n * (n - 1) * (2 * n - 1) / 6;       // a² + b²

        // (a - b)² = 2(a² + b²) - (a + b)²
        let diff = (2 * q - s * s).isqrt();                  // |a - b|

        vec![((s - diff) / 2) as i32, ((s + diff) / 2) as i32]
    }
}
```
