---
title: "leetcode-字符串71"
date: 2026-10-10T12:56:57+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 有效单词

有效单词 需要满足以下几个条件：

至少 包含 3 个字符。
由数字 0-9 和英文大小写字母组成。（不必包含所有这类字符。）
至少 包含一个 元音字母 。
至少 包含一个 辅音字母 。

给你一个字符串 word 。如果 word 是一个有效单词，则返回 true ，否则返回 false 。

注意：

'a'、'e'、'i'、'o'、'u' 及其大写形式都属于 元音字母 。
英文中的 辅音字母 是指那些除元音字母之外的字母。


```
impl Solution {
    pub fn is_valid(word: String) -> bool {
        if word.len() < 3 {
            return false;
        }

        let (mut has_vowel, mut has_consonant) = (false, false);

        for c in word.bytes() {
            match c.to_ascii_lowercase() {
                b'a' | b'e' | b'i' | b'o' | b'u' => has_vowel = true,
                b'a'..=b'z' => has_consonant = true,
                b'0'..=b'9' => {}
                _ => return false,
            }
        }

        has_vowel && has_consonant
    }
}
```
