---
title: "leetcode-字符串44"
date: 2026-10-10T12:56:54+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 句子中的有效单词数

句子仅由小写字母（'a' 到 'z'）、数字（'0' 到 '9'）、连字符（'-'）、标点符号（'!'、'.' 和 ','）以及空格（' '）组成。每个句子可以根据空格分解成 一个或者多个 token ，这些 token 之间由一个或者多个空格 ' ' 分隔。

如果一个 token 同时满足下述条件，则认为这个 token 是一个有效单词：

· 仅由小写字母、连字符和/或标点（不含数字）组成。
· 至多一个 连字符 '-' 。如果存在，连字符两侧应当都存在小写字母（"a-b" 是一个有效单词，但 "-ab" 和 "ab-" 不是有效单词）。
· 至多一个 标点符号。如果存在，标点符号应当位于 token 的 末尾 。

这里给出几个有效单词的例子："a-b."、"afad"、"ba-c"、"a!" 和 "!" 。

给你一个字符串 sentence ，请你找出并返回 sentence 中 有效单词的数目 。


```
impl Solution {
    pub fn count_valid_words(sentence: String) -> i32 {
        sentence
            .split_ascii_whitespace()
            .filter(|w| Self::is_valid(w))
            .count() as i32
    }

    fn is_valid(w: &str) -> bool {
        let b = w.as_bytes();
        let n = b.len();
        let mut hyphen_seen = false;

        for (i, &c) in b.iter().enumerate() {
            match c {
                b'a'..=b'z' => {}
                b'-' => {
                    // 至多一个连字符，不能位于首尾，且两侧必须是小写字母
                    if hyphen_seen || i == 0 || i == n - 1 {
                        return false;
                    }
                    if !b[i - 1].is_ascii_lowercase() || !b[i + 1].is_ascii_lowercase() {
                        return false;
                    }
                    hyphen_seen = true;
                }
                b'!' | b'.' | b',' => {
                    // 标点只能出现在末尾
                    if i != n - 1 {
                        return false;
                    }
                }
                _ => return false, // 数字或其他非法字符
            }
        }
        true
    }
}
```
