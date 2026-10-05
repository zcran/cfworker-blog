---
title: "leetcode-双指针75"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 交替合并字符串

给你两个字符串 word1 和 word2 。请你从 word1 开始，通过交替添加字母来合并字符串。如果一个字符串比另一个字符串长，就将多出来的字母追加到合并后字符串的末尾。

返回 合并后的字符串 。


```
impl Solution {
    pub fn merge_alternately(word1: String, word2: String) -> String {
        let (bytes1, bytes2) = (word1.as_bytes(), word2.as_bytes());
        let (n, m) = (bytes1.len(), bytes2.len());
        let mut result = Vec::with_capacity(n + m);

        // 交替合并两个字符串
        let min_len = n.min(m);
        for i in 0..min_len {
            result.push(bytes1[i]);
            result.push(bytes2[i]);
        }

        // 追加剩余部分
        if n > m {
            result.extend_from_slice(&bytes1[m..]);
        } else if m > n {
            result.extend_from_slice(&bytes2[n..]);
        }

        unsafe { String::from_utf8_unchecked(result) }
    }
}
```
