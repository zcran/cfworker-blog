---
title: "leetcode-字符串匹配9"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 检查单词是否为句中其他单词的前缀

给你一个字符串 sentence 作为句子并指定检索词为 searchWord ，其中句子由若干用 单个空格 分隔的单词组成。请你检查检索词 searchWord 是否为句子 sentence 中任意单词的前缀。

如果 searchWord 是某一个单词的前缀，则返回句子 sentence 中该单词所对应的下标（下标从 1 开始）。如果 searchWord 是多个单词的前缀，则返回匹配的第一个单词的下标（最小下标）。如果 searchWord 不是任何单词的前缀，则返回 -1 。

字符串 s 的 前缀 是 s 的任何前导连续子字符串。


```
impl Solution {
    pub fn is_prefix_of_word(sentence: String, search_word: String) -> i32 {
        sentence
            // 1. 按空白字符分割句子，返回单词迭代器（每个 &str）
            .split_whitespace()
            // 2. 为每个单词附上索引（从 0 开始）
            .enumerate()
            // 3. 查找第一个满足条件的单词
            .find(|(_, word)| {
                // 检查当前单词是否以 search_word 开头
                word.starts_with(&search_word)
            })
            // 4. 若找到，提取其索引并转换为 1-based 的 i32
            .map(|(index, _)| (index + 1) as i32)
            // 5. 若未找到，返回默认值 -1
            .unwrap_or(-1)
    }
}
```
