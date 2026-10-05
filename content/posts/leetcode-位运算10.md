---
title: "leetcode-位运算10"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 两整数之和

给你两个整数 a 和 b ，不使用 运算符 + 和 - ，计算并返回两整数之和。


```
impl Solution {
    /// 核心思路（位运算模拟加法）：
    /// - 不进位的和：a ^ b
    /// - 进位：      (a & b) << 1
    /// 将两者反复相加，直到进位为 0，此时 a ^ b 即为结果。
    ///
    /// 注意：Rust 中 i32 左移溢出在 debug 下会 panic，
    /// 因此先用 u32 做无符号位运算，最后再转回 i32。
    pub fn get_sum(a: i32, b: i32) -> i32 {
        let (mut a, mut b) = (a as u32, b as u32);

        while b != 0 {
            // 进位
            let carry = (a & b) << 1;
            // 不带进位的和
            a ^= b;
            // 把进位加到下一轮
            b = carry;
        }

        a as i32
    }
}
```
