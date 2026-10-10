---
title: "leetcode-哈希表28"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 元音拼写检查器

在给定单词列表 wordlist 的情况下，我们希望实现一个拼写检查器，将查询单词转换为正确的单词。

对于给定的查询单词 query，拼写检查器将会处理两类拼写错误：

· 大小写：如果查询匹配单词列表中的某个单词（不区分大小写），则返回的正确单词与单词列表中的大小写相同。
    · 例如：wordlist = ["yellow"], query = "YellOw": correct = "yellow"
    · 例如：wordlist = ["Yellow"], query = "yellow": correct = "Yellow"
    · 例如：wordlist = ["yellow"], query = "yellow": correct = "yellow"

· 元音错误：如果在将查询单词中的元音 ('a', 'e', 'i', 'o', 'u')  分别替换为任何元音后，能与单词列表中的单词匹配（不区分大小写），则返回的正确单词与单词列表中的匹配项大小写相同。
    · 例如：wordlist = ["YellOw"], query = "yollow": correct = "YellOw"
    · 例如：wordlist = ["YellOw"], query = "yeellow": correct = "" （无匹配项）
    · 例如：wordlist = ["YellOw"], query = "yllw": correct = "" （无匹配项）

此外，拼写检查器还按照以下优先级规则操作：

· 当查询完全匹配单词列表中的某个单词（区分大小写）时，应返回相同的单词。
· 当查询匹配到大小写问题的单词时，您应该返回单词列表中的第一个这样的匹配项。
· 当查询匹配到元音错误的单词时，您应该返回单词列表中的第一个这样的匹配项。
· 如果该查询在单词列表中没有匹配项，则应返回空字符串。

给出一些查询 queries，返回一个单词列表 answer，其中 answer[i] 是由查询 query = queries[i] 得到的正确单词。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    pub fn spellchecker(wordlist: Vec<String>, queries: Vec<String>) -> Vec<String> {
        // 三级匹配表，均按优先级顺序：完全匹配 → 忽略大小写 → 忽略元音。
        // HashMap 的 or_insert 保证只保留 wordlist 中首次出现的单词。
        fn is_vowel(c: u8) -> bool {
            matches!(c, b'a' | b'e' | b'i' | b'o' | b'u')
        }

        // 元音替换为 '*' 后的形式（输入应为小写）。
        fn mask(s: &str) -> String {
            s.bytes()
                .map(|b| if is_vowel(b) { '*' } else { b as char })
                .collect()
        }

        let mut perfect = HashSet::new();
        let mut lower = HashMap::new();
        let mut masked = HashMap::new();

        for word in &wordlist {
            perfect.insert(word.as_str());
            let low = word.to_ascii_lowercase();
            lower.entry(low.clone()).or_insert(word.as_str());
            masked.entry(mask(&low)).or_insert(word.as_str());
        }

        queries
            .iter()
            .map(|q| {
                // 1. 完全匹配
                if perfect.contains(q.as_str()) {
                    return q.clone();
                }
                let low = q.to_ascii_lowercase();
                // 2. 忽略大小写
                if let Some(&w) = lower.get(&low) {
                    return w.to_string();
                }
                // 3. 忽略元音
                if let Some(&w) = masked.get(&mask(&low)) {
                    return w.to_string();
                }
                // 4. 无匹配
                String::new()
            })
            .collect()
    }
}
```
