---
title: "leetcode-字符串22"
date: 2026-10-10T12:56:52+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 生成每种字符都是奇数个的字符串

给你一个整数 n，请你返回一个含 n 个字符的字符串，其中每种字符在该字符串中都恰好出现 奇数次 。

返回的字符串必须只含小写英文字母。如果存在多个满足题目要求的字符串，则返回其中任意一个即可。


```
impl Solution {
    pub fn generate_the_string(n: i32) -> String {
        // 奇数：n 个相同字符，每个都出现奇数次
        // 偶数：1 个 'a' + (n-1) 个 'b'，两种字符各出现奇数次
        if n % 2 == 1 {
            "a".repeat(n as usize)
        } else {
            format!("a{}", "b".repeat(n as usize - 1))
        }
    }
}
```
