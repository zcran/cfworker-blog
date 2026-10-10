---
title: "leetcode-数学21"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 整数的各位积和之差

给你一个整数 n，请你帮忙计算并返回该整数「各位数字之积」与「各位数字之和」的差。


```
impl Solution {
    /// 返回整数 n 的「各位数字之积」与「各位数字之和」的差。
    ///
    /// 逐位取出数字，分别累乘、累加，最后相减即可。
    pub fn subtract_product_and_sum(mut n: i32) -> i32 {
        let mut product = 1;
        let mut sum = 0;

        while n > 0 {
            let digit = n % 10;
            product *= digit;
            sum += digit;
            n /= 10;
        }

        product - sum
    }
}
```
