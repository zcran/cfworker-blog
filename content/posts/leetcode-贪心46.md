---
title: "leetcode-贪心46"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 破坏回文串

给你一个由小写英文字母组成的回文字符串 palindrome ，请你将其中 一个 字符用任意小写英文字母替换，使得结果字符串的 字典序最小 ，且 不是 回文串。

请你返回结果字符串。如果无法做到，则返回一个 空串 。

如果两个字符串长度相同，那么字符串 a 字典序比字符串 b 小可以这样定义：在 a 和 b 出现不同的第一个位置上，字符串 a 中的字符严格小于 b 中的对应字符。例如，"abcc” 字典序比 "abcd" 小，因为不同的第一个位置是在第四个字符，显然 'c' 比 'd' 小。


```
impl Solution {
    pub fn break_palindrome(palindrome: String) -> String {
        let mut data = palindrome.into_bytes();
        let n = data.len();

        // 长度为 1 时改任一字符仍回文，无解
        if n == 1 {
            return String::new();
        }

        // 前半部分找第一个非 'a' 的字符改成 'a'，使其字典序最小
        for i in 0..n / 2 {
            if data[i] != b'a' {
                data[i] = b'a';
                return String::from_utf8(data).unwrap();
            }
        }

        // 前半部分全为 'a'，只能改最后一个字符（对称位置若改会影响前半）
        data[n - 1] = b'b';
        String::from_utf8(data).unwrap()
    }
}
```
