---
title: "leetcode-数学56"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 分类求和并作差

给你两个正整数 n 和 m 。

现定义两个整数 num1 和 num2 ，如下所示：

num1：范围 [1, n] 内所有 无法被 m 整除 的整数之和。
num2：范围 [1, n] 内所有 能够被 m 整除 的整数之和。

返回整数 num1 - num2 。


```
impl Solution {
    pub fn difference_of_sums(n: i32, m: i32) -> i32 {
        // 总和 S = 1 + 2 + ... + n = n(n+1)/2
        // 能被 m 整除的数之和 num2 = m * (1 + 2 + ... + n/m)
        //                              = m * k(k+1)/2，其中 k = n/m
        // num1 - num2 = (S - num2) - num2 = S - 2 * num2
        //             = n(n+1)/2 - m * k(k+1)
        let k = n / m;
        n * (n + 1) / 2 - m * k * (k + 1)
    }
}
```
