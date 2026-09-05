---
title: "leetcode-前缀和45"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最美子字符串的数目

如果某个字符串中 至多一个 字母出现 奇数 次，则称其为 最美 字符串。

例如，"ccjjc" 和 "abab" 都是最美字符串，但 "ab" 不是。

给你一个字符串 word ，该字符串由前十个小写英文字母组成（'a' 到 'j'）。请你返回 word 中 最美非空子字符串 的数目。如果同样的子字符串在 word 中出现多次，那么应当对 每次出现 分别计数。

子字符串 是字符串中的一个连续字符序列。



```
impl Solution {
    /// 计算最美子字符串的数目（使用数组优化版本）
    pub fn wonderful_substrings(word: String) -> i64 {
        // 掩码范围 0..1024 (10位)
        let mut freq = [0; 1024];
        freq[0] = 1;
        let mut mask = 0;
        let mut ans = 0i64;

        for ch in word.bytes() {
            let idx = (ch - b'a') as usize;
            mask ^= 1 << idx;

            // 相同掩码
            ans += freq[mask];

            // 相差一位的掩码
            for i in 0..10 {
                ans += freq[mask ^ (1 << i)];
            }

            freq[mask] += 1;
        }

        ans
    }
}
```
