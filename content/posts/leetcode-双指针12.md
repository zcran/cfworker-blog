---
title: "leetcode-双指针12"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 验证回文串

如果在将所有大写字符转换为小写字符、并移除所有非字母数字字符之后，短语正着读和反着读都一样。则可以认为该短语是一个 回文串 。

字母和数字都属于字母数字字符。

给你一个字符串 s，如果它是 回文串 ，返回 true ；否则，返回 false 。

```
impl Solution {
    /// 判断字符串是否为回文串（只考虑字母数字字符，忽略大小写）
    /// 时间复杂度 O(n)，空间复杂度 O(1)
    /// 双指针从两端向中间移动，跳过非字母数字字符
    pub fn is_palindrome(s: String) -> bool {
        let s = s.as_bytes();
        let (mut left, mut right) = (0, s.len().saturating_sub(1));

        while left < right {
            if !s[left].is_ascii_alphanumeric() {
                left += 1;
            } else if !s[right].is_ascii_alphanumeric() {
                right -= 1;
            } else if s[left].to_ascii_lowercase() == s[right].to_ascii_lowercase() {
                left += 1;
                right -= 1;
            } else {
                return false;
            }
        }

        true
    }
}
```
