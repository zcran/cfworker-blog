---
title: "leetcode-二分查找26"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 第 N 个神奇数字

一个正整数如果能被 a 或 b 整除，那么它是神奇的。

给定三个整数 n , a , b ，返回第 n 个神奇的数字。因为答案可能很大，所以返回答案 对 109 + 7 取模 后的值。


```
impl Solution {
    /// 返回第 n 个能被 a 或 b 整除的正整数，并对 1e9+7 取模。
    ///
    /// 思路：设 f(x) = x 以内能被 a 或 b 整除的数的个数
    ///            = x/a + x/b - x/lcm(a,b)
    /// f(x) 关于 x 单调递增，二分找最小的 x 使 f(x) >= n，即为答案。
    ///
    /// 注意：
    ///   - 中间计算用 i64 防止溢出。
    ///   - lcm 通过 gcd 求得：lcm = a / gcd(a,b) * b。
    pub fn nth_magical_number(n: i32, a: i32, b: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let (a, b) = (a as i64, b as i64);

        // 计算 gcd
        let mut x = a;
        let mut y = b;
        while y != 0 {
            let t = x % y;
            x = y;
            y = t;
        }
        let gcd = x;
        let lcm = a / gcd * b;

        // f(x)：x 以内能被 a 或 b 整除的数的个数
        let count = |x: i64| x / a + x / b - x / lcm;

        // 二分找最小 x 使 count(x) >= n
        let (mut lo, mut hi) = (1, n as i64 * a.min(b));
        while lo < hi {
            let mid = lo + (hi - lo) / 2;
            if count(mid) < n as i64 {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }

        (lo % MOD) as i32
    }
}
```
