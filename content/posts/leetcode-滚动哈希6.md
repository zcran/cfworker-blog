---
title: "leetcode-滚动哈希6"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 不同的循环子字符串

给你一个字符串 text ，请你返回满足下述条件的 不同 非空子字符串的数目：

可以写成某个字符串与其自身相连接的形式（即，可以写为 a + a，其中 a 是某个字符串）。

例如，abcabc 就是 abc 和它自身连接形成的。


```
use std::collections::HashSet;

impl Solution {
    /// 返回字符串 `text` 中所有不同的“双倍”子串的数量。
    /// “双倍”子串定义为可以写成 `a + a` 形式的非空子串。
    ///
    /// # 算法
    /// 1. 使用滚动哈希（Rabin–Karp）预处理字符串，支持 O(1) 获取任意子串哈希。
    /// 2. 枚举所有可能的偶数长度子串，比较前半和后半的哈希值。
    /// 3. 用哈希集合存储所有满足条件的子串（通过双哈希值唯一标识），最后返回集合大小。
    ///
    /// # 参数
    /// - `text`: 输入字符串
    ///
    /// # 返回值
    /// - 不同“双倍”子串的数量
    pub fn distinct_echo_substrings(text: String) -> i32 {
        let s = text.as_bytes();
        let n = s.len();
        if n < 2 {
            return 0;
        }

        // ---------- 滚动哈希参数 ----------
        const BASE: u64 = 131;              // 基数（通常取 131 或 137）
        const MOD1: u64 = 1_000_000_007;
        const MOD2: u64 = 1_000_000_009;

        // 预计算幂
        let mut pow1 = vec![1u64; n + 1];
        let mut pow2 = vec![1u64; n + 1];
        for i in 1..=n {
            pow1[i] = (pow1[i - 1] * BASE) % MOD1;
            pow2[i] = (pow2[i - 1] * BASE) % MOD2;
        }

        // 前缀哈希（双模）
        let mut pref1 = vec![0u64; n + 1];
        let mut pref2 = vec![0u64; n + 1];
        for (i, &ch) in s.iter().enumerate() {
            let val = (ch - b'a' + 1) as u64; // 映射为 1..26
            pref1[i + 1] = (pref1[i] * BASE + val) % MOD1;
            pref2[i + 1] = (pref2[i] * BASE + val) % MOD2;
        }

        // 子串哈希获取闭包（半开区间 [l, r)）
        let get_hash = |l: usize, r: usize| -> (u64, u64) {
            let len = r - l;
            let h1 = (pref1[r] + MOD1 - (pref1[l] * pow1[len]) % MOD1) % MOD1;
            let h2 = (pref2[r] + MOD2 - (pref2[l] * pow2[len]) % MOD2) % MOD2;
            (h1, h2)
        };

        // 存储所有已发现的不同“双倍”子串的哈希值（双哈希元组）
        let mut seen = HashSet::new();

        // 枚举所有可能的子串长度（偶数）
        for len in (2..=n).step_by(2) {
            let half = len / 2;
            // 枚举所有起始位置
            for start in 0..=(n - len) {
                // 前半部分 [start, start+half)，后半部分 [start+half, start+len)
                let left_hash = get_hash(start, start + half);
                let right_hash = get_hash(start + half, start + len);
                if left_hash == right_hash {
                    // 满足条件，记录该子串（用哈希值代表）
                    seen.insert(left_hash); // 前后哈希相等，任意一个即可代表整个子串
                }
            }
        }

        seen.len() as i32
    }
}
```
