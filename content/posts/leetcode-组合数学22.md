---
title: "leetcode-组合数学22"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 重新排列后包含指定子字符串的字符串数目


给你一个整数 n 。

如果一个字符串 s 只包含小写英文字母，且 将 s 的字符重新排列后，新字符串包含 子字符串 "leet" ，那么我们称字符串 s 是一个 好 字符串。

比方说：

字符串 "lteer" 是好字符串，因为重新排列后可以得到 "leetr" 。
"letl" 不是好字符串，因为无法重新排列并得到子字符串 "leet" 。
请你返回长度为 n 的好字符串 总 数目。

由于答案可能很大，将答案对 10^9 + 7 取余 后返回。

子字符串 是一个字符串中一段连续的字符序列。



```
impl Solution {
    pub fn string_count(n: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = n as i64;
        // 长度小于 4 时无法包含子串 "leet"
        if n < 4 {
            return 0;
        }

        // 快速幂取模 (base^exp % MOD)
        fn pow_mod(mut base: i64, mut exp: i64) -> i64 {
            let mut res = 1;
            while exp > 0 {
                if exp & 1 == 1 {
                    res = (res * base) % MOD;
                }
                base = (base * base) % MOD;
                exp >>= 1;
            }
            res
        }

        // 容斥原理计算至少包含 1个'l', 2个'e', 1个't' 的字符串个数
        let a = pow_mod(26, n);                       // 全部字符串
        let b = pow_mod(25, n - 1) * (75 + n) % MOD;  // 缺少至少一个必要字符（容斥第一层）
        let c = pow_mod(24, n - 1) * (72 + 2 * n) % MOD; // 缺少至少两个必要字符（容斥第二层）
        let d = pow_mod(23, n - 1) * (23 + n) % MOD;  // 缺少全部三个必要字符集（容斥第三层）

        let ans = (a - b + c - d) % MOD;
        ((ans + MOD) % MOD) as i32
    }
}
```
