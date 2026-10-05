---
title: "leetcode-位运算71"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 仅含置位位的最小整数

给你一个正整数 n。

返回 大于等于 n 且二进制表示仅包含 置位 位的 最小 整数 x 。

置位 位指的是二进制表示中值为 1 的位。


```
impl Solution {
    pub fn smallest_number(n: i32) -> i32 {
        // 目标 x 的二进制全为 1，即形如 1, 11, 111, ... (2^m - 1)
        // 找到最小的 m 使得 2^m - 1 >= n
        // 用位运算：取 n 的最高位，构造覆盖该位的全 1 掩码
        let mut x = 0;
        let mut n = n;
        while n > 0 {
            x = (x << 1) | 1;
            n >>= 1;
        }
        x
    }
}
```
