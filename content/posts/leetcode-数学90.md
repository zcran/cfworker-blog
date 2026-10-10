---
title: "leetcode-数学90"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 有效数字整数

给你一个整数 n 和一个数字 x。

如果一个数字满足以下条件，则认为它是 有效 的：

它包含 至少一个 数字 x，并且
它 不以 数字 x 开头。

如果 n 是 有效 的，请返回 true，否则返回 false。


```
impl Solution {
    pub fn valid_digit(n: i32, x: i32) -> bool {
        // 检查除最高位外的所有数字中是否包含 x
        let mut has_x = false;
        let mut n = n;
        while n >= 10 {
            if n % 10 == x {
                has_x = true;
            }
            n /= 10;
        }
        // 最高位（此时 n 为单个数字）不能等于 x
        has_x && n != x
    }
}
```
