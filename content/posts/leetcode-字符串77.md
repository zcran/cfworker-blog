---
title: "leetcode-字符串77"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 找出长度为 K 的特殊子字符串

给你一个字符串 s 和一个整数 k。

判断是否存在一个长度 恰好 为 k 的子字符串，该子字符串需要满足以下条件：

1. 该子字符串 只包含一个唯一字符（例如，"aaa" 或 "bbb"）。
2. 如果该子字符串的 前面 有字符，则该字符必须与子字符串中的字符不同。
3. 如果该子字符串的 后面 有字符，则该字符也必须与子字符串中的字符不同。

如果存在这样的子串，返回 true；否则，返回 false。

子字符串 是字符串中的连续、非空字符序列。


```
impl Solution {
    pub fn has_special_substring(s: String, k: i32) -> bool {
        // chunk_by 按相邻相同字符合并，只要存在长度恰好为 k 的块即可
        s.as_bytes()
            .chunk_by(|a, b| a == b)
            .any(|chunk| chunk.len() == k as usize)
    }
}
```
