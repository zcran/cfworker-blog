---
title: "leetcode-数学37"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## K 进制表示下的各位数字总和

给你一个整数 n（10 进制）和一个基数 k ，请你将 n 从 10 进制表示转换为 k 进制表示，计算并返回转换后各位数字的 总和 。

转换后，各位数字应当视作是 10 进制数字，且它们的总和也应当按 10 进制表示返回。


```
impl Solution {
    pub fn sum_base(mut n: i32, k: i32) -> i32 {
        // 不断取 n 除以 k 的余数，即为 k 进制下的各位数字。
        let mut sum = 0;
        while n > 0 {
            sum += n % k;
            n /= k;
        }
        sum
    }
}
```
