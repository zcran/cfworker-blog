---
title: "leetcode-动态规划19"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 整数拆分

给定一个正整数 n ，将其拆分为 k 个 正整数 的和（ k >= 2 ），并使这些整数的乘积最大化。

返回 你可以获得的最大乘积 。


```
impl Solution {
    pub fn integer_break(n: i32) -> i32 {
        // n = 2 或 3 时，只能拆成 1 + (n-1)，最大乘积为 n-1
        if n < 4 {
            return n - 1;
        }

        // 数学结论：尽可能多地拆出 3 能使乘积最大
        // 余数 0 -> 全 3；余数 1 -> 把 3+1 换成 2+2；余数 2 -> 保留一个 2
        let mut ans = 1;
        let mut n = n;
        while n > 4 {
            ans *= 3;
            n -= 3;
        }
        ans * n
    }
}
```
