---
title: "leetcode-双指针40"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 验证回文串 II

给你一个字符串 s，最多 可以从中删除一个字符。

请你判断 s 是否能成为回文字符串：如果能，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn valid_palindrome(s: String) -> bool {
        let s = s.as_bytes();
        let (mut left, mut right) = (0, s.len() - 1);

        while left < right {
            if s[left] != s[right] {
                // 尝试删除左字符或右字符，检查剩余部分是否为回文
                return Self::is_palindrome(s, left + 1, right)
                    || Self::is_palindrome(s, left, right - 1);
            }
            left += 1;
            right -= 1;
        }

        true
    }

    /// 检查 s[left..=right] 是否为回文
    fn is_palindrome(s: &[u8], mut left: usize, mut right: usize) -> bool {
        while left < right {
            if s[left] != s[right] {
                return false;
            }
            left += 1;
            right -= 1;
        }
        true
    }
}
```
