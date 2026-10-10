---
title: "leetcode-字符串47"
date: 2026-10-10T12:56:55+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 将标题首字母大写

给你一个字符串 title ，它由单个空格连接一个或多个单词组成，每个单词都只包含英文字母。请你按以下规则将每个单词的首字母 大写 ：

如果单词的长度为 1 或者 2 ，所有字母变成小写。
否则，将单词首字母大写，剩余字母变成小写。

请你返回 大写后 的 title 。


```
impl Solution {
    pub fn capitalize_title(title: String) -> String {
        title
            .split_ascii_whitespace()
            .map(|w| {
                if w.len() > 2 {
                    // 首字母大写，其余小写
                    let mut s = String::with_capacity(w.len());
                    s.push(w.as_bytes()[0].to_ascii_uppercase() as char);
                    s.push_str(&w[1..].to_ascii_lowercase());
                    s
                } else {
                    w.to_ascii_lowercase()
                }
            })
            .collect::<Vec<_>>()
            .join(" ")
    }
}
```
