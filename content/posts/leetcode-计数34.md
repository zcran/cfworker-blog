---
title: "leetcode-计数34"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 判断字符串的两半是否相似

给你一个偶数长度的字符串 s 。将其拆分成长度相同的两半，前一半为 a ，后一半为 b 。

两个字符串 相似 的前提是它们都含有相同数目的元音（'a'，'e'，'i'，'o'，'u'，'A'，'E'，'I'，'O'，'U'）。注意，s 可能同时含有大写和小写字母。

如果 a 和 b 相似，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn halves_are_alike(s: String) -> bool {
        // 判断字符是否为元音的辅助函数
        fn is_vowel(c: char) -> bool {
            matches!(c, 'a' | 'e' | 'i' | 'o' | 'u' | 'A' | 'E' | 'I' | 'O' | 'U')
        }

        let bytes = s.as_bytes();
        let half = bytes.len() / 2;

        // 同时遍历前后两半，一次遍历统计差异
        let diff = bytes[..half]
            .iter()
            .zip(&bytes[half..])
            .fold(0, |acc, (&left, &right)| {
                let left_vowel = is_vowel(left as char);
                let right_vowel = is_vowel(right as char);
                acc + (left_vowel as i32) - (right_vowel as i32)
            });

        diff == 0
    }
}
```
