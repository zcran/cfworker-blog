---
title: "leetcode-双指针71"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 分割两个字符串得到回文串

给你两个字符串 a 和 b ，它们长度相同。请你选择一个下标，将两个字符串都在 相同的下标 分割开。由 a 可以得到两个字符串： aˇprefix 和 aˇsuffix ，满足 a = aˇprefix + aˇsuffix ，同理，由 b 可以得到两个字符串 bˇprefix 和 bˇsuffix ，满足 b = bˇprefix + bˇsuffix 。请你判断 aˇprefix + bˇsuffix 或者 bˇprefix + aˇsuffix 能否构成回文串。

当你将一个字符串 s 分割成 sˇprefix 和 sˇsuffix 时， sˇsuffix 或者 sˇprefix 可以为空。比方说， s = "abc" 那么 "" + "abc" ， "a" + "bc" ， "ab" + "c" 和 "abc" + "" 都是合法分割。

如果 能构成回文字符串 ，那么请返回 true，否则返回 false 。

注意， x + y 表示连接字符串 x 和 y 。


```
impl Solution {
    pub fn check_palindrome_formation(a: String, b: String) -> bool {
        let a = a.as_bytes();
        let b = b.as_bytes();
        can_form(a, b) || can_form(b, a)
    }
}

/// 检查 a_prefix + b_suffix 是否能构成回文
fn can_form(a: &[u8], b: &[u8]) -> bool {
    let (mut i, mut j) = (0, a.len() - 1);

    // 从两端匹配 a 的前缀和 b 的后缀
    while i < j && a[i] == b[j] {
        i += 1;
        j -= 1;
    }

    // 如果已经匹配完，说明整体是回文
    i >= j || is_palindrome(a, i, j) || is_palindrome(b, i, j)
}

/// 检查子串 s[i..=j] 是否为回文
fn is_palindrome(s: &[u8], mut i: usize, mut j: usize) -> bool {
    while i < j && s[i] == s[j] {
        i += 1;
        j -= 1;
    }
    i >= j
}
```
