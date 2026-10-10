---
title: "leetcode-字符串74"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 找出加密后的字符串

给你一个字符串 s 和一个整数 k。请你使用以下算法加密字符串：

对于字符串 s 中的每个字符 c，用字符串中 c 后面的第 k 个字符替换 c（以循环方式）。

返回加密后的字符串。


```
impl Solution {
    pub fn get_encrypted_string(mut s: String, k: i32) -> String {
        format!("{}{}", s.split_off(k as usize % s.len()), s)
    }
}
```
