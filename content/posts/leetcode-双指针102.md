---
title: "leetcode-双指针102"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 字典序最小回文串

给你一个由 小写英文字母 组成的字符串 s ，你可以对其执行一些操作。在一步操作中，你可以用其他小写英文字母 替换  s 中的一个字符。

请你执行 尽可能少的操作 ，使 s 变成一个 回文串 。如果执行 最少 操作次数的方案不止一种，则只需选取 字典序最小 的方案。

对于两个长度相同的字符串 a 和 b ，在 a 和 b 出现不同的第一个位置，如果该位置上 a 中对应字母比 b 中对应字母在字母表中出现顺序更早，则认为 a 的字典序比 b 的字典序要小。

返回最终的回文字符串。


```
impl Solution {
    /// 将字符串变为字典序最小的回文串，且操作次数最少。
    ///
    /// 思路：对称位置只需统一成两者中较小的字符，
    /// 即可保证最少替换次数，同时使字典序最小。
    /// 时间 O(n)，空间 O(n)。
    pub fn make_smallest_palindrome(s: String) -> String {
        let mut bytes = s.into_bytes();
        let n = bytes.len();

        for i in 0..n / 2 {
            let j = n - 1 - i;
            let min = bytes[i].min(bytes[j]);
            bytes[i] = min;
            bytes[j] = min;
        }

        // 全部为小写英文字母，转回 String 不会失败
        String::from_utf8(bytes).unwrap()
    }
}
```
