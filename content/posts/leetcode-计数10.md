---
title: "leetcode-计数10"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 两句话中的不常见单词

句子 是一串由空格分隔的单词。每个 单词 仅由小写字母组成。

如果某个单词在其中一个句子中恰好出现一次，在另一个句子中却 没有出现 ，那么这个单词就是 不常见的 。

给你两个 句子 s1 和 s2 ，返回所有 不常用单词 的列表。返回列表中单词可以按 任意顺序 组织。


```
impl Solution {
    pub fn uncommon_from_sentences(s1: String, s2: String) -> Vec<String> {
        use std::collections::HashMap;

        // 统计两个句子中所有单词的出现次数
        let mut freq = HashMap::new();
        for word in s1.split_ascii_whitespace().chain(s2.split_ascii_whitespace()) {
            *freq.entry(word).or_insert(0) += 1;
        }

        // 出现次数为 1 的单词即为不常见单词
        freq.into_iter()
            .filter(|(_, count)| *count == 1)
            .map(|(word, _)| word.to_string())
            .collect()
    }
}
```
