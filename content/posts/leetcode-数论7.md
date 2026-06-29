---
title: "leetcode-数论7"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 最简分数

给你一个整数 n ，请你返回所有 0 到 1 之间（不包括 0 和 1）满足分母小于等于  n 的 最简 分数 。分数可以以 任意 顺序返回。


```
impl Solution {
    /// 返回所有 0 到 1 之间的最简分数，分母 <= n
    ///
    /// 核心思路：枚举所有分母 i（2..=n），枚举分子 j（1..i-1），
    /// 如果 gcd(j, i) == 1，则 j/i 是最简分数
    pub fn simplified_fractions(n: i32) -> Vec<String> {
        let mut result = Vec::new();

        // 枚举分母
        for denominator in 2..=n {
            // 枚举分子（只到 denominator-1，保证分数 < 1）
            for numerator in 1..denominator {
                // 检查是否互质（最简分数）
                if Self::gcd(numerator, denominator) == 1 {
                    result.push(format!("{}/{}", numerator, denominator));
                }
            }
        }

        result
    }

    /// 计算最大公约数（欧几里得算法）
    #[inline]
    fn gcd(a: i32, b: i32) -> i32 {
        if b == 0 {
            a
        } else {
            Self::gcd(b, a % b)
        }
    }
}
```
