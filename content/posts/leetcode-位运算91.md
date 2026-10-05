---
title: "leetcode-位运算91"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 加密运算

计算机安全专家正在开发一款高度安全的加密通信软件，需要在进行数据传输时对数据进行加密和解密操作。假定 dataA 和 dataB 分别为随机抽样的两次通信的数据量：

正数为发送量
负数为接受量
0 为数据遗失

请不使用四则运算符的情况下实现一个函数计算两次通信的数据量之和（三种情况均需被统计），以确保在数据传输过程中的高安全性和保密性。




```
impl Solution {
    pub fn encryption_calculate(a: i32, b: i32) -> i32 {
        // 不使用四则运算符，用位运算模拟加法：
        //   无进位和  = a ^ b
        //   进位      = (a & b) << 1
        // 不断把进位加到无进位和上，直到进位为 0。
        //
        // 注意：Rust 中 i32 溢出在 debug 下会 panic，这里用 wrapping 系列
        // 保证与 C++/Java 的 int 溢出行为一致（平台要求 32 位补码语义）。
        let mut a = a;
        let mut b = b;
        while b != 0 {
            let carry = (a & b).wrapping_shl(1);
            a = a ^ b;
            b = carry;
        }
        a
    }
}
```
