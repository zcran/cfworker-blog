---
title: "leetcode-贪心186"
date: 2026-10-03T12:55:46+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 验证回文串 II

给定一个非空字符串 s，请判断如果 最多 从字符串中删除一个字符能否得到一个回文字符串。


```
impl Solution {
    pub fn valid_palindrome(s: String) -> bool {
        // 双指针从两端向中间比较。
        // 遇到不相等时，尝试删掉左边或右边一个字符，检查剩余子串是否为回文。
        let b = s.as_bytes();
        let (mut l, mut r) = (0, b.len() - 1);

        while l < r {
            if b[l] == b[r] {
                l += 1;
                r -= 1;
            } else {
                // 跳过左字符 或 跳过右字符，只要有一种可行即可
                return Self::is_palindrome(b, l + 1, r) || Self::is_palindrome(b, l, r - 1);
            }
        }
        true
    }

    // 判断 b[lo..=hi] 是否为回文
    fn is_palindrome(b: &[u8], mut lo: usize, mut hi: usize) -> bool {
        while lo < hi {
            if b[lo] != b[hi] {
                return false;
            }
            lo += 1;
            hi -= 1;
        }
        true
    }
}
```
