---
title: "leetcode-组合数学18"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 统计同位异构字符串数目

给你一个字符串 s ，它包含一个或者多个单词。单词之间用单个空格 ' ' 隔开。

如果字符串 t 中第 i 个单词是 s 中第 i 个单词的一个 排列 ，那么我们称字符串 t 是字符串 s 的同位异构字符串。

比方说，"acb dfe" 是 "abc def" 的同位异构字符串，但是 "def cab" 和 "adc bef" 不是。

请你返回 s 的同位异构字符串的数目，由于答案可能很大，请你将它对 10^9 + 7 取余 后返回。


```
impl Solution {
    const MOD: i64 = 1_000_000_007;

    /// 快速幂：计算 x^n mod MOD
    fn pow_mod(mut x: i64, mut n: i64) -> i64 {
        let mut res = 1;
        while n > 0 {
            if n & 1 == 1 {
                res = (res * x) % Self::MOD;
            }
            x = (x * x) % Self::MOD;
            n >>= 1;
        }
        res
    }

    pub fn count_anagrams(s: String) -> i32 {
        let mut ans = 1i64;      // 累乘所有单词长度的阶乘 j!
        let mut mul = 1i64;      // 累乘每个单词中各字母出现次数的阶乘 cnt!
        let mut cnt = [0; 26];   // 当前单词的字母频率
        let mut word_len = 0;    // 当前单词已处理的字符数

        for &b in s.as_bytes() {
            if b == b' ' {
                // 单词结束，重置状态
                cnt = [0; 26];
                word_len = 0;
            } else {
                let idx = (b - b'a') as usize;
                cnt[idx] += 1;
                word_len += 1;
                // ans *= word_len
                ans = (ans * (word_len as i64)) % Self::MOD;
                // mul *= cnt[idx]
                mul = (mul * (cnt[idx] as i64)) % Self::MOD;
            }
        }

        // ans * inv(mul) % MOD
        (ans * Self::pow_mod(mul, Self::MOD - 2) % Self::MOD) as i32
    }
}
```
