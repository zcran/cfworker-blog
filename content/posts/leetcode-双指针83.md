---
title: "leetcode-双指针83"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 检查字符串是否为数组前缀

给你一个字符串 s 和一个字符串数组 words ，请你判断 s 是否为 words 的 前缀字符串 。

字符串 s 要成为 words 的 前缀字符串 ，需要满足：s 可以由 words 中的前 k（k 为 正数 ）个字符串按顺序相连得到，且 k 不超过 words.length 。

如果 s 是 words 的 前缀字符串 ，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn is_prefix_string(s: String, words: Vec<String>) -> bool {
        let s = s.as_bytes();
        let mut pos = 0;

        // 按顺序拼接 words 中的字符串，直到超过或无法匹配 s
        for word in &words {
            let w = word.as_bytes();
            if s[pos..].starts_with(w) {
                pos += w.len();
                // 提前返回：已经完整匹配 s
                if pos == s.len() {
                    return true;
                }
            } else {
                break;
            }
        }

        pos == s.len()
    }
}
```
