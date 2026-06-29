---
title: "leetcode-数论3"
date: 2026-06-20T11:09:54+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 回文质数

给你一个整数 n ，返回大于或等于 n 的最小 回文质数。

一个整数如果恰好有两个除数：1 和它本身，那么它是 质数 。注意，1 不是质数。

· 例如，2、3、5、7、11 和 13 都是质数。

一个整数如果从左向右读和从右向左读是相同的，那么它是 回文数 。

· 例如，101 和 12321 都是回文数。

测试用例保证答案总是存在，并且在 \[2, 2 * 10^8\] 范围内。


```
impl Solution {
    pub fn prime_palindrome(n: i32) -> i32 {
        // ---------- 1. 处理小数字 ----------
        if n <= 2 {
            return 2;
        }
        if n <= 3 {
            return 3;
        }
        if n <= 5 {
            return 5;
        }
        if n <= 7 {
            return 7;
        }
        if n <= 11 {
            return 11;
        }

        // ---------- 2. 偶数长度的回文数都能被 11 整除 ----------
        // 除了 11 本身，所有偶数长度的回文数都不是质数
        let s = n.to_string();
        let len = s.len();

        let mut start_len = if len % 2 == 0 { len + 1 } else { len };

        loop {
            let half_len = (start_len + 1) / 2;
            let start = 10_i32.pow((half_len - 1) as u32);

            for prefix in start..10_i32.pow(half_len as u32) {
                // 检查回文数的第一位（即 prefix 的第一位）是否为偶数
                // 如果是偶数，则整个回文数是偶数，不可能是质数
                let first_digit = prefix / 10_i32.pow((half_len - 1) as u32);
                if first_digit % 2 == 0 {
                    continue;
                }

                let palindrome = Self::build_palindrome(prefix, start_len);
                if palindrome >= n && Self::is_prime(palindrome) {
                    return palindrome;
                }
            }

            start_len += 2;
        }
    }

    #[inline]
    fn build_palindrome(prefix: i32, len: usize) -> i32 {
        let s = prefix.to_string();
        let rev: String = s.chars().rev().collect();

        let suffix = if len % 2 == 1 {
            &rev[1..]
        } else {
            &rev[..]
        };

        (s + suffix).parse().unwrap()
    }

    #[inline]
    fn is_prime(x: i32) -> bool {
        if x < 2 {
            return false;
        }
        if x % 2 == 0 {
            return x == 2;
        }
        if x % 3 == 0 {
            return x == 3;
        }

        let limit = (x as f64).sqrt() as i32;
        let mut i = 5;
        while i <= limit {
            if x % i == 0 || x % (i + 2) == 0 {
                return false;
            }
            i += 6;
        }
        true
    }
}
```
