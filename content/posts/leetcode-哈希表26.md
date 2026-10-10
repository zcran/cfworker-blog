---
title: "leetcode-哈希表26"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 验证外星语词典

某种外星语也使用英文小写字母，但可能顺序 order 不同。字母表的顺序（order）是一些小写字母的排列。

给定一组用外星语书写的单词 words，以及其字母表的顺序 order，只有当给定的单词在这种外星语中按字典序排列时，返回 true；否则，返回 false。




```
impl Solution {
    pub fn is_alien_sorted(words: Vec<String>, order: String) -> bool {
        // 用数组记录每个字母在外星字母表中的位次，映射为可比较的数值。
        let mut rank = [0u8; 26];
        for (i, b) in order.bytes().enumerate() {
            rank[(b - b'a') as usize] = i as u8;
        }

        // 将每个单词转换成位次序列，再直接按字典序比较相邻单词。
        // Rust 的 Vec 比较天然支持前缀更短的排在前，无需额外处理。
        let ranked: Vec<Vec<u8>> = words
            .iter()
            .map(|w| w.bytes().map(|b| rank[(b - b'a') as usize]).collect())
            .collect();

        ranked.windows(2).all(|pair| pair[0] <= pair[1])
    }
}
```
