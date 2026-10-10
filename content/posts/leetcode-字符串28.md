---
title: "leetcode-字符串28"
date: 2026-10-10T12:56:53+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 替换所有的问号

给你一个仅包含小写英文字母和 '?' 字符的字符串 s，请你将所有的 '?' 转换为若干小写字母，使最终的字符串不包含任何 连续重复 的字符。

注意：你 不能 修改非 '?' 字符。

题目测试用例保证 除 '?' 字符 之外，不存在连续重复的字符。

在完成所有转换（可能无需转换）后返回最终的字符串。如果有多个解决方案，请返回其中任何一个。可以证明，在给定的约束条件下，答案总是存在的。


```
impl Solution {
    pub fn modify_string(s: String) -> String {
        // SAFETY: 全程按 ASCII 小写字母和 '?' 操作，转回 String 不会破坏 UTF-8
        let mut bytes = s.into_bytes();

        for i in 0..bytes.len() {
            if bytes[i] != b'?' {
                continue;
            }

            let left = if i > 0 { Some(bytes[i - 1]) } else { None };
            let right = if i + 1 < bytes.len() { Some(bytes[i + 1]) } else { None };

            // 只需从 'a' 开始试，最多试 3 个字母必有一个可用
            for c in b'a'..=b'c' {
                if Some(c) != left && Some(c) != right {
                    bytes[i] = c;
                    break;
                }
            }
        }

        // SAFETY: 原始输入是合法 UTF-8，且只替换为 ASCII 字母
        unsafe { String::from_utf8_unchecked(bytes) }
    }
}
```
