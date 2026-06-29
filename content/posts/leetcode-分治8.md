---
title: "leetcode-分治8"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 位1的个数

给定一个正整数 n，编写一个函数，获取一个正整数的二进制形式并返回其二进制表达式中 设置位 的个数（也被称为汉明重量）。


```
impl Solution {
    pub fn hamming_weight(n: i32) -> i32 {
        // 将 i32 视为无符号 32 位整数进行位运算
        (n as u32).count_ones() as i32
    }
}
```
