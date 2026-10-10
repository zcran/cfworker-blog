---
title: "leetcode-数学27"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 最接近的因数

给你一个整数 num，请你找出同时满足下面全部要求的两个整数：

两数乘积等于  num + 1 或 num + 2
以绝对差进行度量，两数大小最接近

你可以按任意顺序返回这两个整数。




```
impl Solution {
    /// 找出乘积等于 num+1 或 num+2 且两者最接近的一对整数。
    ///
    /// 对目标值 n，从 ⌊√n⌋ 向下找第一个能整除 n 的数 d，
    /// 则 (d, n/d) 就是最接近的一对因数（因数越接近乘积越大）。
    /// 分别对 num+1 和 num+2 求这对因数，取差更小的一组。
    pub fn closest_divisors(num: i32) -> Vec<i32> {
        let a = Self::closest_pair(num + 1);
        let b = Self::closest_pair(num + 2);
        if (a[0] - a[1]).abs() <= (b[0] - b[1]).abs() {
            a
        } else {
            b
        }
    }

    /// 返回乘积为 n、且两因数最接近的一对 (d, n/d)。
    fn closest_pair(n: i32) -> Vec<i32> {
        // 从 √n 向下枚举，找到第一个整除 n 的因数
        let mut d = (n as f64).sqrt() as i32;
        while d > 1 && n % d != 0 {
            d -= 1;
        }
        vec![d, n / d]
    }
}
```
