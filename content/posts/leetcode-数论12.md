---
title: "leetcode-数论12"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 三除数

给你一个整数 n 。如果 n 恰好有三个正除数 ，返回 true ；否则，返回 false 。

如果存在整数 k ，满足 n = k * m ，那么整数 m 就是 n 的一个 除数 。


```
impl Solution {
    /// 判断一个整数是否恰好有三个正除数
    ///
    /// 数学原理：
    /// 一个数恰好有 3 个除数，当且仅当它是一个质数的平方
    /// 例如：4 = 2² → 除数：1, 2, 4
    ///      9 = 3² → 除数：1, 3, 9
    ///
    /// 证明：
    /// - 如果 n 有三个正除数，其因子分解为 p²（p 为质数）
    /// - 因为只有质数的平方才会有恰好 3 个除数：1, p, p²
    pub fn is_three(n: i32) -> bool {
        // 边界条件：n < 4 时，2 有 2 个除数，3 有 2 个除数，1 只有 1 个除数
        if n < 4 {
            return false;
        }

        // 求 n 的平方根
        let root = (n as f64).sqrt() as i32;

        // 如果不是完全平方数，则不可能有 3 个除数
        if root * root != n {
            return false;
        }

        // 检查平方根是否为质数
        Self::is_prime(root)
    }

    /// 判断一个数是否为质数
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
        // 只检查 6k ± 1 形式的数
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
