---
title: "leetcode-字符串41"
date: 2026-10-10T12:56:54+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 删除字符使字符串变好

一个字符串如果没有 三个连续 相同字符，那么它就是一个 好字符串 。

给你一个字符串 s ，请你从 s 删除 最少 的字符，使它变成一个 好字符串 。

请你返回删除后的字符串。题目数据保证答案总是 唯一的 。


```
impl Solution {
    pub fn make_fancy_string(s: String) -> String {
        let mut result = String::with_capacity(s.len());
        for b in s.bytes() {
            // 若末尾已有两个相同字符且与当前字符相同，则跳过
            let n = result.len();
            if n >= 2 && result.as_bytes()[n - 1] == b && result.as_bytes()[n - 2] == b {
                continue;
            }
            result.push(b as char);
        }
        result
    }
}
```
