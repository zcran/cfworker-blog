---
title: "leetcode-位运算25"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 或运算的最小翻转次数

给你三个正整数 a、b 和 c。

你可以对 a 和 b 的二进制表示进行位翻转操作，返回能够使按位或运算   a OR b == c  成立的最小翻转次数。

「位翻转操作」是指将一个数的二进制表示任何单个位上的 1 变成 0 或者 0 变成 1 。


```
impl Solution {
    /// 返回使 `a | b == c` 所需的最小位翻转次数。
    ///
    /// 逐位分析（a、b、c 的对应位分别为 x、y、z）：
    /// - 若 z == 1：需要 x 或 y 至少一个为 1。若 x = y = 0，翻转一次即可。
    /// - 若 z == 0：需要 x = y = 0。翻转次数为 x + y。
    ///
    /// 综合两种情况，每位翻转数为 `((x | y) ^ z) + (x & y & !z)`。
    pub fn min_flips(mut a: i32, mut b: i32, mut c: i32) -> i32 {
        let mut flips = 0;
        while a > 0 || b > 0 || c > 0 {
            let x = a & 1;
            let y = b & 1;
            let z = c & 1;
            flips += ((x | y) ^ z) + (x & y & !z);
            a >>= 1;
            b >>= 1;
            c >>= 1;
        }
        flips
    }
}
```
