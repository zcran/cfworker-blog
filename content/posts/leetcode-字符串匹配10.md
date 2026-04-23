---
title: "leetcode-字符串匹配10"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 最大重复子字符串

给你一个字符串 sequence ，如果字符串 word 连续重复 k 次形成的字符串是 sequence 的一个子字符串，那么单词 word 的 重复值为 k 。单词 word 的 最大重复值 是单词 word 在 sequence 中最大的重复值。如果 word 不是 sequence 的子串，那么重复值 k 为 0 。

给你一个字符串 sequence 和 word ，请你返回 最大重复值 k 。


```
impl Solution {
    pub fn max_repeating(sequence: String, word: String) -> i32 {
        let mut s = word.clone();
        let mut res = 0;
        while sequence.contains(&s) {
            s = s + &word;
            res += 1;
        }
        res
    }
}

```
