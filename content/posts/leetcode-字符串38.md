---
title: "leetcode-字符串38"
date: 2026-10-10T12:56:54+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 将所有数字用字符替换

给你一个下标从 0 开始的字符串 s ，它的 偶数 下标处为小写英文字母，奇数 下标处为数字。

定义一个函数 shift(c, x) ，其中 c 是一个字符且 x 是一个数字，函数返回字母表中 c 后面第 x 个字符。

比方说，shift('a', 5) = 'f' 和 shift('x', 0) = 'x' 。

对于每个 奇数 下标 i ，你需要将数字 s[i] 用 shift(s[i-1], s[i]) 替换。

请你替换所有数字以后，将字符串 s 返回。题目 保证 shift(s[i-1], s[i]) 不会超过 'z' 。




```
impl Solution {
    pub fn replace_digits(s: String) -> String {
        // 原地修改：奇数位数字 = 前一位字母 + 该数字
        let mut bytes = s.into_bytes();
        let mut i = 1;
        while i < bytes.len() {
            bytes[i] += bytes[i - 1] - b'0';
            i += 2;
        }
        // SAFETY: 所有字节仍为合法 ASCII
        unsafe { String::from_utf8_unchecked(bytes) }
    }
}
```
