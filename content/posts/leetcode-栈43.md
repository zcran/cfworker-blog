---
title: "leetcode-栈43"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 反转单词前缀

给你一个下标从 0 开始的字符串 word 和一个字符 ch 。找出 ch 第一次出现的下标 i ，反转 word 中从下标 0 开始、直到下标 i 结束（含下标 i ）的那段字符。如果 word 中不存在字符 ch ，则无需进行任何操作。

例如，如果 word = "abcdefd" 且 ch = 'd' ，那么你应该 反转 从下标 0 开始、直到下标 3 结束（含下标 3 ）。结果字符串将会是 "dcbaefd" 。

返回 结果字符串 。


```
impl Solution {
    pub fn reverse_prefix(word: String, ch: char) -> String {
        if let Some(pos) = word.find(ch) {
            let mut result = String::with_capacity(word.len());
            // 反转前缀部分
            for c in word[..=pos].chars().rev() {
                result.push(c);
            }
            // 追加剩余部分
            result.push_str(&word[pos + 1..]);
            result
        } else {
            word
        }
    }
}
```
