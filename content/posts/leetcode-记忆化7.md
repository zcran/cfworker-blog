---
title: "leetcode-记忆化7"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---



## 斐波那契数


斐波那契数 （通常用 F(n) 表示）形成的序列称为 斐波那契数列 。该数列由 0 和 1 开始，后面的每一项数字都是前面两项数字的和。也就是：

F(0) = 0，F(1) = 1
F(n) = F(n - 1) + F(n - 2)，其中 n > 1

给定 n ，请计算 F(n) 。

```
impl Solution {
    pub fn fib(n: i32) -> i32 {
        let n = n as usize;
        let mut f = vec![0; n + 2];
        f[1] = 1;
        for i in 2..=n {
            f[i] = f[i - 1] + f[i - 2];
        }
        f[n]
    }
}
```
