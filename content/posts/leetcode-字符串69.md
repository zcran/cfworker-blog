---
title: "leetcode-字符串69"
date: 2026-10-10T12:56:57+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 按键变更的次数

给你一个下标从 0 开始的字符串 s ，该字符串由用户输入。按键变更的定义是：使用与上次使用的按键不同的键。例如 s = "ab" 表示按键变更一次，而 s = "bBBb" 不存在按键变更。

返回用户输入过程中按键变更的次数。

注意：shift 或 caps lock 等修饰键不计入按键变更，也就是说，如果用户先输入字母 'a' 然后输入字母 'A' ，不算作按键变更。


```
impl Solution {
    pub fn count_key_changes(s: String) -> i32 {
        s.as_bytes()
            .windows(2)
            .filter(|w| w[0].to_ascii_lowercase() != w[1].to_ascii_lowercase())
            .count() as i32
    }
}
```
