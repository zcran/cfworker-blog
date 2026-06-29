---
title: "leetcode-分治7"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 颠倒二进制位


颠倒给定的 32 位有符号整数的二进制位。


```
impl Solution {
    pub fn reverse_bits(n: i32) -> i32 {
        let mut x = n;
        let mut rev = 0;

        for i in 0..32 {
            if x == 0 {
                break;
            }
            rev |= (x & 1) << (31 - i);
            x >>= 1;
        }

        rev
    }
}
```
