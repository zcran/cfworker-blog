---
title: "leetcode-动态规划80"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 恢复数组

某个程序本来应该输出一个整数数组。但是这个程序忘记输出空格了以致输出了一个数字字符串，我们所知道的信息只有：数组中所有整数都在 [1, k] 之间，且数组中的数字都没有前导 0 。

给你字符串 s 和整数 k 。可能会有多种不同的数组恢复结果。

按照上述程序，请你返回所有可能输出字符串 s 的数组方案数。

由于数组方案数可能会很大，请你返回它对 10^9 + 7 取余 后的结果。


```
impl Solution {
    /// 1416. 恢复数组
    ///
    /// dp[i]：字符串 s 的前 i 个字符能恢复出的合法数组方案数。
    /// 枚举最后一段数字 s[j..i]（j 从 i-1 向前），若该段无前导 0 且值 ≤ k，
    /// 则 dp[i] += dp[j]。
    ///
    /// 由于 k ≤ 10^9，一段数字最多 10 位就会超过 k，内层循环至多 10 次，
    /// 总时间 O(10n)，空间 O(n)。
    pub fn number_of_arrays(s: String, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let bytes = s.as_bytes();
        let n = bytes.len();
        let k = k as i64;

        let mut dp = vec![0i64; n + 1];
        dp[0] = 1;

        for i in 1..=n {
            let mut num = 0i64;      // 当前枚举的后缀数值
            let mut base = 1i64;     // 10 的幂
            // 从 i-1 向前枚举起点 j，最多 10 位
            for j in (0..i).rev() {
                if i - j > 10 {
                    break;
                }
                num += (bytes[j] - b'0') as i64 * base;
                if num > k {
                    break;
                }
                // 无前导 0 才合法
                if bytes[j] != b'0' {
                    dp[i] = (dp[i] + dp[j]) % MOD;
                }
                base *= 10;
            }
        }

        dp[n] as i32
    }
}
```
