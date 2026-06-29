---
title: "leetcode-数论22"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 公因子的数目

给你两个正整数 a 和 b ，返回 a 和 b 的 公 因子的数目。

如果 x 可以同时整除 a 和 b ，则认为 x 是 a 和 b 的一个 公因子 。


```
impl Solution {
    pub fn common_factors(a: i32, b: i32) -> i32 {
        // 计算两个数的最大公约数
        let g = gcd(a, b);

        // 最大公约数的因子个数就是公共因子个数
        // 枚举 1 到 sqrt(g) 寻找因子
        let mut count = 0;
        let mut i = 1;
        while i * i <= g {
            if g % i == 0 {
                count += 1;
                if i * i != g {
                    count += 1;
                }
            }
            i += 1;
        }
        count
    }
}

/// 欧几里得算法计算最大公约数（迭代版本）
#[inline]
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a
}
```
