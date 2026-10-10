---
title: "leetcode-字符串21"
date: 2026-10-10T12:56:52+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 解码字母到整数映射

给你一个字符串 s，它由数字（'0' - '9'）和 '#' 组成。我们希望按下述规则将 s 映射为一些小写英文字符：

字符（'a' - 'i'）分别用（'1' - '9'）表示。
字符（'j' - 'z'）分别用（'10#' - '26#'）表示。

返回映射之后形成的新字符串。

题目数据保证映射始终唯一。


```
impl Solution {
    pub fn freq_alphabets(s: String) -> String {
        let bytes = s.as_bytes();
        let n = bytes.len();
        let mut result = String::with_capacity(n);
        let mut i = 0;

        while i < n {
            // 形如 "10#"~"26#"：当前字节后跟 '#'，取两位数字映射到 'j'~'z'
            if i + 2 < n && bytes[i + 2] == b'#' {
                let num = (bytes[i] - b'0') * 10 + (bytes[i + 1] - b'0');
                result.push((b'a' + num - 1) as char);
                i += 3;
            } else {
                // 单个数字 '1'~'9' 映射到 'a'~'i'
                result.push((b'a' + bytes[i] - b'0' - 1) as char);
                i += 1;
            }
        }

        result
    }
}
```
