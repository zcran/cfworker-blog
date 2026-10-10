---
title: "leetcode-动态规划72"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计元音字母序列的数目

给你一个整数 n，请你帮忙统计一下我们可以按下述规则形成多少个长度为 n 的字符串：

字符串中的每个字符都应当是小写元音字母（'a', 'e', 'i', 'o', 'u'）
每个元音 'a' 后面都只能跟着 'e'
每个元音 'e' 后面只能跟着 'a' 或者是 'i'
每个元音 'i' 后面 不能 再跟着另一个 'i'
每个元音 'o' 后面只能跟着 'i' 或者是 'u'
每个元音 'u' 后面只能跟着 'a'

由于答案可能会很大，所以请你返回 模 10^9 + 7 之后的结果。


```
impl Solution {
    /// 1220. 统计元音字母序列的数目
    ///
    /// 状态转移（每个字母后能接的字母）：
    ///   a → e
    ///   e → a, i
    ///   i → a, e, o, u
    ///   o → i, u
    ///   u → a
    ///
    /// 递推式（dp[x] 表示以 x 结尾、长度当前的序列数）：
    ///   new_a = e + i + u
    ///   new_e = a + i
    ///   new_i = e + o
    ///   new_o = i
    ///   new_u = i + o
    ///
    /// 初始长度为 1 时每个字母各 1 种。迭代 n-1 次，答案为五个状态之和。
    /// 用 5 个变量代替矩阵快速幂，代码更短、常数更小，O(n) 时间、O(1) 空间。
    pub fn count_vowel_permutation(n: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 分别对应 a, e, i, o, u
        let (mut a, mut e, mut i, mut o, mut u) = (1i64, 1, 1, 1, 1);

        for _ in 1..n {
            let (na, ne, ni, no, nu) = (
                (e + i + u) % MOD, // a 前可接 e, i, u
                (a + i) % MOD,     // e 前可接 a, i
                (e + o) % MOD,     // i 前可接 e, o
                i,                 // o 前可接 i
                (i + o) % MOD,     // u 前可接 i, o
            );
            a = na;
            e = ne;
            i = ni;
            o = no;
            u = nu;
        }

        ((a + e + i + o + u) % MOD) as i32
    }
}
```
