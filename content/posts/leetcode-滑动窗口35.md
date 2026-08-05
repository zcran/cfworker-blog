---
title: "leetcode-滑动窗口35"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 定长子串中元音的最大数目

给你字符串 s 和整数 k 。

请返回字符串 s 中长度为 k 的单个子字符串中可能包含的最大元音字母数。

英文中的 元音字母 为（a, e, i, o, u）。


```
impl Solution {
    pub fn max_vowels(s: String, k: i32) -> i32 {
        let s = s.as_bytes();
        let k = k as usize;
        let mut vowel_count = 0;
        let mut max_count = 0;

        // 辅助判断是否为元音
        let is_vowel = |c: u8| matches!(c, b'a' | b'e' | b'i' | b'o' | b'u');

        // 先统计第一个窗口中的元音数
        for i in 0..k {
            if is_vowel(s[i]) {
                vowel_count += 1;
            }
        }
        max_count = vowel_count;

        // 滑动窗口
        for i in k..s.len() {
            // 新字符进入窗口
            if is_vowel(s[i]) {
                vowel_count += 1;
            }
            // 旧字符离开窗口
            if is_vowel(s[i - k]) {
                vowel_count -= 1;
            }
            max_count = max_count.max(vowel_count);
        }

        max_count
    }
}
```
