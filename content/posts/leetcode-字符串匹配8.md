---
title: "leetcode-字符串匹配8"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 数组中的字符串匹配

给你一个字符串数组 words ，数组中的每个字符串都可以看作是一个单词。请你按 任意 顺序返回 words 中是其他单词的 子字符串 的所有单词。

```
impl Solution {
    pub fn string_matching(words: Vec<String>) -> Vec<String> {
        let sentence = words.join(" ");
        words.into_iter().filter(|word| sentence.matches(word).nth(1).is_some()).collect::<Vec<_>>()
    }
}
```



若要严格遵循“不能是自身”的定义，可使用以下修正版：

impl Solution {
    pub fn string_matching(words: Vec<String>) -> Vec<String> {
        let sentence = words.join(" ");
        words
            .into_iter()
            .filter(|word| {
                // 找到第一个匹配的起始索引
                if let Some(first) = sentence.find(word) {
                    // 在剩余部分中再次查找，若找到说明存在另一个匹配
                    sentence[first + 1..].contains(word)
                } else {
                    false
                }
            })
            .collect()
    }
}
