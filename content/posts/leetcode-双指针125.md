---
title: "leetcode-双指针125"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 反转字符串前缀

给你一个字符串 s 和一个整数 k。

反转 s 的前 k 个字符，并返回结果字符串。


```
impl Solution {
    pub fn reverse_prefix(s: String, k: i32) -> String {
        // 转为字节数组原地反转前 k 个字符（题目保证 s 仅含小写字母，按字节处理安全）
        let mut bytes = s.into_bytes();
        let k = k as usize;
        bytes[..k].reverse();
        // 字节数组是合法 UTF-8，可直接转回 String
        String::from_utf8(bytes).unwrap()
    }
}
```
