---
title: "leetcode-位运算14"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 汉明距离

两个整数之间的 汉明距离 指的是这两个数字对应二进制位不同的位置的数目。

给你两个整数 x 和 y，计算并返回它们之间的汉明距离。


```
impl Solution {
    /// 核心思路：
    /// 两数异或后，结果中为 1 的位恰好是 x 与 y 不同的位，
    /// 因此统计 (x ^ y) 中 1 的个数即为汉明距离。
    ///
    /// `count_ones` 是编译器内建指令（通常映射到 POPCNT），速度极快。
    pub fn hamming_distance(x: i32, y: i32) -> i32 {
        (x ^ y).count_ones() as i32
    }
}
```
