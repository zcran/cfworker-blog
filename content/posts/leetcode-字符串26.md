---
title: "leetcode-字符串26"
date: 2026-10-10T12:56:53+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 重新排列字符串

给你一个字符串 s 和一个 长度相同 的整数数组 indices 。

请你重新排列字符串 s ，其中第 i 个字符需要移动到 indices[i] 指示的位置。

返回重新排列后的字符串。


```
impl Solution {
    pub fn restore_string(s: String, indices: Vec<i32>) -> String {
        // 按目标位置直接放置字符，避免排序
        let mut result = vec![0u8; s.len()];
        for (ch, &i) in s.bytes().zip(indices.iter()) {
            result[i as usize] = ch;
        }
        String::from_utf8(result).unwrap()
    }
}
```
