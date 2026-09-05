---
title: "leetcode-前缀和20"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 每个元音包含偶数次的最长子字符串

给你一个字符串 s ，请你返回满足以下条件的最长子字符串的长度：每个元音字母，即 'a'，'e'，'i'，'o'，'u' ，在子字符串中都恰好出现了偶数次。


```
impl Solution {
    pub fn find_the_longest_substring(s: String) -> i32 {
        // 用 5 位二进制掩码表示 5 个元音的奇偶状态
        // 第 0 位: a, 第 1 位: e, 第 2 位: i, 第 3 位: o, 第 4 位: u
        let mut first_occurrence = [-1; 32]; // 32 种状态
        first_occurrence[0] = 0; // 初始状态（全偶数）出现在索引 0

        let mut mask = 0;
        let mut max_len = 0;

        for (i, ch) in s.bytes().enumerate() {
            // 根据当前字符更新掩码
            mask ^= match ch {
                b'a' => 1 << 0,
                b'e' => 1 << 1,
                b'i' => 1 << 2,
                b'o' => 1 << 3,
                b'u' => 1 << 4,
                _ => 0,
            };

            // 如果该状态之前出现过，则这段子串的元音都是偶数次
            if first_occurrence[mask] >= 0 {
                let len = (i + 1) - first_occurrence[mask] as usize;
                if len > max_len {
                    max_len = len;
                }
            } else {
                first_occurrence[mask] = (i + 1) as i32; // 存储索引位置（前缀长度）
            }
        }

        max_len as i32
    }
}
```
