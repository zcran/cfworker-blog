---
title: "leetcode-滚动哈希5"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 段式回文

你会得到一个字符串 text 。你应该把它分成 k 个子字符串 (subtext1, subtext2，…， subtextk) ，要求满足:

·subtexti 是 非空 字符串
·所有子字符串的连接等于 text ( 即subtext1 + subtext2 + ... + subtextk == text )
·对于所有 i 的有效值( 即 1 <= i <= k ) ，subtexti == subtextk - i + 1 均成立

返回k可能最大值。


```
use std::collections::HashMap;

impl Solution {
    /// 返回将字符串 `text` 分解成回文子串序列的最大块数。
    /// 使用**滚动哈希（Rabin–Karp）** + **贪心递归**。
    ///
    /// # 原理
    /// - 要最大化块数，每次应从两端匹配尽可能短的相等子串。
    /// - 滚动哈希 O(1) 比较任意子串，避免扫描。
    /// - 双哈希（两个大质数模数）消除碰撞风险。
    ///
    /// # 参数
    /// - `text`: 输入字符串
    ///
    /// # 返回值
    /// - 最大块数
    pub fn longest_decomposition(text: String) -> i32 {
        let bytes = text.as_bytes();
        let n = bytes.len();
        if n == 0 {
            return 0;
        }

        // 哈希参数（双模）
        const BASE: u64 = 91138233;
        const MOD1: u64 = 1_000_000_007;
        const MOD2: u64 = 1_000_000_009;

        // 预计算幂
        let mut pow1 = vec![1u64; n + 1];
        let mut pow2 = vec![1u64; n + 1];
        for i in 1..=n {
            pow1[i] = (pow1[i - 1] * BASE) % MOD1;
            pow2[i] = (pow2[i - 1] * BASE) % MOD2;
        }

        // 前缀哈希
        let mut pref1 = vec![0u64; n + 1];
        let mut pref2 = vec![0u64; n + 1];
        for (i, &ch) in bytes.iter().enumerate() {
            let val = (ch - b'a' + 1) as u64; // 1..26
            pref1[i + 1] = (pref1[i] * BASE + val) % MOD1;
            pref2[i + 1] = (pref2[i] * BASE + val) % MOD2;
        }

        // 子串哈希获取函数（返回双哈希值）
        let get_hash = |l: usize, r: usize| -> (u64, u64) {
            // 区间 [l, r) 的半开区间，r 为结束索引（不包含）
            let len = r - l;
            let h1 = (pref1[r] + MOD1 - (pref1[l] * pow1[len]) % MOD1) % MOD1;
            let h2 = (pref2[r] + MOD2 - (pref2[l] * pow2[len]) % MOD2) % MOD2;
            (h1, h2)
        };

        // 递归求解（使用闭包捕获哈希函数，避免重复传递）
        fn solve(
            l: usize,
            r: usize, // r 是右边界索引（包含），区间为 [l, r]
            get_hash: &dyn Fn(usize, usize) -> (u64, u64),
            bytes: &[u8],
        ) -> i32 {
            if l > r {
                return 0;
            }
            if l == r {
                return 1;
            }
            let len_range = (r - l + 1) as usize;
            // 尝试所有可能的前缀长度（从 1 到区间长度的一半）
            for len in 1..=(len_range / 2) {
                // 前缀 [l, l+len-1] 与后缀 [r-len+1, r] 比较
                let prefix_hash = get_hash(l, l + len);
                let suffix_hash = get_hash(r - len + 1, r + 1); // 注意右边界开区间
                if prefix_hash == suffix_hash {
                    // 找到最短的匹配，贪心分割
                    return 2 + solve(l + len, r - len, get_hash, bytes);
                }
            }
            // 未找到任何匹配，整个区间作为一块
            1
        }

        solve(0, n - 1, &get_hash, &bytes)
    }
}
```
