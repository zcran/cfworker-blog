---
title: "leetcode-位运算101"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 不用加号的加法

设计一个函数把两个数字相加。不得使用 + 或者其他算术运算符。

```
impl Solution {
    pub fn add(a: i32, b: i32) -> i32 {
        // 不用 + 等算术运算符，用位运算模拟加法：
        //   无进位和 = a ^ b
        //   进位     = (a & b) << 1
        // 把进位不断加到无进位和上，直到进位为 0。
        //
        // 注意：Rust 中 i32 左移/溢出在 debug 下会 panic，
        // 这里用 wrapping_shl / wrapping_add 保证 32 位补码语义。
        let mut a = a;
        let mut b = b;
        while b != 0 {
            let carry = (a & b).wrapping_shl(1);
            a ^= b;
            b = carry;
        }
        a
    }
}
```
