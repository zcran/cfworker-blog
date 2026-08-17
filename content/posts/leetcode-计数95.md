---
title: "leetcode-计数95"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 统计以给定字符开头和结尾的子字符串总数

给你一个字符串 s 和一个字符 c 。返回在字符串 s 中并且以 c 字符开头和结尾的非空子字符串的总数。


```
impl Solution {
    pub fn count_substrings(s: String, c: char) -> i64 {
        // 核心：以 c 开头结尾的子串数 = 选两个 c 的位置（可相同）= C(n,2) + n = n*(n+1)/2
        let n = s.bytes().filter(|&b| b == c as u8).count() as i64;
        n * (n + 1) / 2
    }
}
```
