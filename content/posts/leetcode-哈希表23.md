---
title: "leetcode-哈希表23"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 查找和替换模式

你有一个单词列表 words 和一个模式  pattern，你想知道 words 中的哪些单词与模式匹配。

如果存在字母的排列 p ，使得将模式中的每个字母 x 替换为 p(x) 之后，我们就得到了所需的单词，那么单词与模式是匹配的。

（回想一下，字母的排列是从字母到字母的双射：每个字母映射到另一个字母，没有两个字母映射到同一个字母。）

返回 words 中与给定模式匹配的单词列表。

你可以按任何顺序返回答案。


```
impl Solution {
    pub fn find_and_replace_pattern(words: Vec<String>, pattern: String) -> Vec<String> {
        // 双向映射检查：word 与 pattern 必须同构（字符到字符的双射）。
        // 用两个数组记录每个字母上次出现的位置，位置一致则映射一致。
        let pat = pattern.as_bytes();

        let is_match = |word: &str| -> bool {
            let w = word.as_bytes();
            if w.len() != pat.len() {
                return false;
            }
            let mut w_pos = [0u8; 26];
            let mut p_pos = [0u8; 26];
            for (i, (&wc, &pc)) in w.iter().zip(pat).enumerate() {
                let (wi, pi) = ((wc - b'a') as usize, (pc - b'a') as usize);
                // 用 i+1 作为"上次出现位置"标记，0 表示未出现。
                if w_pos[wi] != p_pos[pi] {
                    return false;
                }
                w_pos[wi] = i as u8 + 1;
                p_pos[pi] = i as u8 + 1;
            }
            true
        };

        words.into_iter().filter(|w| is_match(w)).collect()
    }
}
```
