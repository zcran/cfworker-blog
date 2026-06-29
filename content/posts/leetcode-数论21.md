---
title: "leetcode-数论21"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 最小偶倍数


给你一个正整数 n ，返回 2 和 n 的最小公倍数（正整数）。

```
impl Solution {
    pub fn smallest_even_multiple(n: i32) -> i32 {
        (n % 2 + 1) * n
    }
}
```
