---
title: "leetcode-数学13"
date: 2026-10-07T20:39:28+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 字符串的最大公因子

对于字符串 s 和 t，只有在 s = t + t + t + ... + t + t（t 自身连接 1 次或多次）时，我们才认定 “t 能除尽 s”。

给定两个字符串 str1 和 str2 。返回 最长字符串 x，要求满足 x 能除尽 str1 且 x 能除尽 str2 。


```
impl Solution {
    /// 返回能同时整除 str1 和 str2 的最长字符串。
    ///
    /// 两个字符串存在公共整除串的必要条件是 `str1 + str2 == str2 + str1`。
    /// 在此条件下，最长公共整除串的长度为两字符串长度的最大公约数，
    /// 即答案就是 `str1` 的前 gcd(len1, len2) 个字符。
    pub fn gcd_of_strings(str1: String, str2: String) -> String {
        // 拼接交换比较：判断两串是否由同一基串重复构成
        if format!("{str1}{str2}") != format!("{str2}{str1}") {
            return String::new();
        }

        // 答案长度为两串长度的 gcd
        let g = Self::gcd(str1.len(), str2.len());
        str1[..g].to_owned()
    }

    /// 使用二进制 GCD（Stein 算法），比取模版更快。
    const fn gcd(mut u: usize, mut v: usize) -> usize {
        if u == 0 {
            return v;
        }
        if v == 0 {
            return u;
        }

        // 提出公因子 2^shift
        let shift = (u | v).trailing_zeros();
        u >>= shift;
        v >>= shift;
        u >>= u.trailing_zeros();

        loop {
            v >>= v.trailing_zeros();

            // 保持 u <= v
            if u > v {
                std::mem::swap(&mut u, &mut v);
            }

            v -= u;
            if v == 0 {
                break;
            }
        }

        u << shift
    }
}
```
