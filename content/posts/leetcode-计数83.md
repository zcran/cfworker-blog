---
title: "leetcode-计数83"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 统计范围内的元音字符串数

给你一个下标从 0 开始的字符串数组 words 和两个整数：left 和 right 。

如果字符串以元音字母开头并以元音字母结尾，那么该字符串就是一个 元音字符串 ，其中元音字母是 'a'、'e'、'i'、'o'、'u' 。

返回 words[i] 是元音字符串的数目，其中 i 在闭区间 [left, right] 内。


```
impl Solution {
    pub fn vowel_strings(words: Vec<String>, left: i32, right: i32) -> i32 {
        // 元音集合，用于快速查找
        let vowels = [b'a', b'e', b'i', b'o', b'u'];

        words[left as usize..=right as usize]
            .iter()
            .filter(|s| {
                let bytes = s.as_bytes();
                // 检查首尾字符是否为元音（直接比较字节，避免UTF-8解码开销）
                vowels.contains(&bytes[0]) && vowels.contains(&bytes[bytes.len() - 1])
            })
            .count() as i32
    }
}
```
