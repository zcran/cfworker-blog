---
title: "leetcode-字符串51"
date: 2026-10-10T12:56:55+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 字母在字符串中的百分比

给你一个字符串 s 和一个字符 letter ，返回在 s 中等于 letter 字符所占的 百分比 ，向下取整到最接近的百分比。


```
impl Solution {
    pub fn percentage_letter(s: String, letter: char) -> i32 {
        // 统计匹配字符数，再按百分比向下取整
        let cnt = s.bytes().filter(|&b| b == letter as u8).count();
        100 * cnt as i32 / s.len() as i32
    }
}
```
