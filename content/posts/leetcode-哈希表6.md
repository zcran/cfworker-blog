---
title: "leetcode-哈希表6"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 单词规律

给定一种规律 pattern 和一个字符串 s ，判断 s 是否遵循相同的规律。

这里的 遵循 指完全匹配，例如， pattern 里的每个字母和字符串 s 中的每个非空单词之间存在着双向连接的对应规律。具体来说：

pattern 中的每个字母都 恰好 映射到 s 中的一个唯一单词。
s 中的每个唯一单词都 恰好 映射到 pattern 中的一个字母。
没有两个字母映射到同一个单词，也没有两个单词映射到同一个字母。


```
use std::collections::HashMap;

impl Solution {
    pub fn word_pattern(pattern: String, s: String) -> bool {
        // 双向映射：pattern 字母 ↔ 单词，任一方向冲突即不匹配。
        // 注意：空单词不算，split_whitespace 已按此语义切分。
        let words: Vec<&str> = s.split_whitespace().collect();
        let chars: Vec<char> = pattern.chars().collect();
        if words.len() != chars.len() {
            return false;
        }

        let mut p2w: HashMap<char, &str> = HashMap::new();
        let mut w2p: HashMap<&str, char> = HashMap::new();
        for (&c, &w) in chars.iter().zip(words.iter()) {
            // 检查两个方向是否都与已有映射一致。
            if p2w.get(&c).is_some_and(|&prev| prev != w)
                || w2p.get(w).is_some_and(|&prev| prev != c)
            {
                return false;
            }
            p2w.insert(c, w);
            w2p.insert(w, c);
        }
        true
    }
}
```
