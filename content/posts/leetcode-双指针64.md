---
title: "leetcode-双指针64"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 删除回文子序列

给你一个字符串 s，它仅由字母 'a' 和 'b' 组成。每一次删除操作都可以从 s 中删除一个回文 子序列。

返回删除给定字符串中所有字符（字符串为空）的最小删除次数。

「子序列」定义：如果一个字符串可以通过删除原字符串某些字符而不改变原字符顺序得到，那么这个字符串就是原字符串的一个子序列。

「回文」定义：如果一个字符串向后和向前读是一致的，那么这个字符串就是一个回文。


```
impl Solution {
    pub fn remove_palindrome_sub(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();

        // 检查整个字符串是否为回文
        // 由于只包含 'a' 和 'b'，非回文情况下最多需要 2 次删除
        // 第一次删除所有 'a'，第二次删除所有 'b'（或反之）
        if bytes.iter().zip(bytes.iter().rev()).all(|(a, b)| a == b) {
            1
        } else {
            2
        }
    }
}
```
