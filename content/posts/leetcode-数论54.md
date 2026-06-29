---
title: "leetcode-数论54"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 最大质数子字符串之和

给定一个字符串 s，找出可以由其 子字符串 组成的 3个最大的不同质数 的和。

返回这些质数的 总和 ，如果少于 3 个不同的质数，则返回 所有 不同质数的和。

质数是大于 1 且只有两个因数的自然数：1和它本身。

子字符串 是字符串中的一个连续字符序列。

注意：每个质数即使出现在 多个 子字符串中，也只能计算 一次 。此外，将子字符串转换为整数时，忽略任何前导零。


```
impl Solution {
    pub fn sum_of_largest_primes(s: String) -> i64 {
        let chars: Vec<char> = s.chars().collect();
        let n = chars.len();
        let mut primes = Vec::new();

        // 枚举所有子字符串
        for i in 0..n {
            // 优化：如果当前数字太大，可以提前终止
            let mut num = 0i64;
            for j in i..n {
                num = num * 10 + (chars[j] as u8 - b'0') as i64;

                // 如果数字超过 i64 范围或者太大，终止（避免无限增长）
                if num > 100_000_000_000_000_0 { // 10^15，适合质数检测
                    break;
                }

                if Self::is_prime(num) {
                    primes.push(num);
                }
            }
        }

        // 去重并降序排序
        primes.sort_unstable();
        primes.dedup();
        primes.reverse();

        // 取前 3 个（如果不足 3 个则取全部）求和
        let count = primes.len().min(3);
        primes.iter().take(count).sum()
    }

    /// 判断一个数是否为质数
    #[inline]
    fn is_prime(n: i64) -> bool {
        if n < 2 {
            return false;
        }
        if n == 2 || n == 3 {
            return true;
        }
        if n % 2 == 0 || n % 3 == 0 {
            return false;
        }

        // 使用 6k ± 1 优化
        let limit = (n as f64).sqrt() as i64;
        let mut i = 5;
        while i <= limit {
            if n % i == 0 || n % (i + 2) == 0 {
                return false;
            }
            i += 6;
        }
        true
    }
}
```
