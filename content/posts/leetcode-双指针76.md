---
title: "leetcode-双指针76"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 句子相似性 III

给定两个字符串 sentence1 和 sentence2，每个表示由一些单词组成的一个句子。句子是一系列由 单个 空格分隔的 单词，且开头和结尾没有多余空格。每个单词都只包含大写和小写英文字母。

如果两个句子 s1 和 s2 ，可以通过往其中一个句子插入一个任意的句子（可以是空句子）而得到另一个句子，那么我们称这两个句子是 相似的 。注意，插入的句子必须与现有单词用空白隔开。

比方说，

s1 = "Hello Jane" 与 s2 = "Hello my name is Jane"，我们可以往 s1 中 "Hello" 和 "Jane" 之间插入 "my name is" 得到 s2 。

s1 = "Frog cool" 与 s2 = "Frogs are cool" 不是相似的，因为尽管往 s1 中插入 "s are"，它没有与 "Frog" 用空格隔开。

给你两个句子 sentence1 和 sentence2 ，如果 sentence1 和 sentence2 是 相似 的，请你返回 true ，否则返回 false 。


```
impl Solution {
    pub fn are_sentences_similar(sentence1: String, sentence2: String) -> bool {
        let words1: Vec<&str> = sentence1.split_ascii_whitespace().collect();
        let words2: Vec<&str> = sentence2.split_ascii_whitespace().collect();

        let (n, m) = (words1.len(), words2.len());
        let (mut left, mut right) = (0, 0);

        // 从左侧匹配相同的单词
        while left < n && left < m && words1[left] == words2[left] {
            left += 1;
        }

        // 从右侧匹配相同的单词
        while right < n - left && right < m - left && words1[n - 1 - right] == words2[m - 1 - right] {
            right += 1;
        }

        // 如果匹配的前缀和后缀覆盖了至少一个句子的所有单词，则相似
        left + right >= n.min(m)
    }
}
```
