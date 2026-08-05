---
title: "leetcode-枚举70"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 最小可整除数位乘积 I

给你两个整数 n 和 t 。请你返回大于等于 n 的 最小 整数，且该整数的 各数位之积 能被 t 整除。


```
impl Solution {
    pub fn smallest_number(n: i32, t: i32) -> i32 {
        let mut num = n;
        loop {
            // 计算各数位之积
            let mut prod = 1;
            let mut x = num;
            while x > 0 {
                prod *= x % 10;
                x /= 10;
            }

            // 如果乘积能被 t 整除，返回当前数字
            if prod % t == 0 {
                return num;
            }

            num += 1;
        }
    }
}
```
