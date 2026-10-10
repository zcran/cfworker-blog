---
title: "leetcode-字符串23"
date: 2026-10-10T12:56:52+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 重新格式化字符串

给你一个混合了数字和字母的字符串 s，其中的字母均为小写英文字母。

请你将该字符串重新格式化，使得任意两个相邻字符的类型都不同。也就是说，字母后面应该跟着数字，而数字后面应该跟着字母。

请你返回 重新格式化后 的字符串；如果无法按要求重新格式化，则返回一个 空字符串 。


```
impl Solution {
    pub fn reformat(s: String) -> String {
        // 分离数字和字母（用字节，避免 char 的 4 字节开销）
        let (digits, letters): (Vec<u8>, Vec<u8>) = s.bytes().partition(u8::is_ascii_digit);

        // 数量差超过 1 则无法交替排列
        if (digits.len() as isize - letters.len() as isize).abs() > 1 {
            return String::new();
        }

        // 让较多的一类先手，保证交替
        let (longer, shorter) = if digits.len() >= letters.len() {
            (digits, letters)
        } else {
            (letters, digits)
        };

        let mut result = String::with_capacity(s.len());
        // 交替推送：longer[0], shorter[0], longer[1], shorter[1], ...
        for i in 0..shorter.len() {
            result.push(longer[i] as char);
            result.push(shorter[i] as char);
        }
        // longer 比 shorter 多一个时，补上最后一个
        if longer.len() > shorter.len() {
            result.push(*longer.last().unwrap() as char);
        }

        result
    }
}
```
