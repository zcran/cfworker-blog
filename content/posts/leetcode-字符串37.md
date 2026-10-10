---
title: "leetcode-字符串37"
date: 2026-10-10T12:56:54+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 截断句子

句子 是一个单词列表，列表中的单词之间用单个空格隔开，且不存在前导或尾随空格。每个单词仅由大小写英文字母组成（不含标点符号）。

例如，"Hello World"、"HELLO" 和 "hello world hello world" 都是句子。

给你一个句子 s 和一个整数 k ，请你将 s 截断 ，使截断后的句子仅含 前 k个单词。返回 截断 s 后得到的句子。




```
impl Solution {
    pub fn truncate_sentence(s: String, k: i32) -> String {
        // 找到第 k 个单词结束的位置，直接截断原串，避免额外分配
        let mut count = 0;
        for (i, b) in s.bytes().enumerate() {
            if b == b' ' {
                count += 1;
                if count == k {
                    return s[..i].to_string();
                }
            }
        }
        s
    }
}
```
