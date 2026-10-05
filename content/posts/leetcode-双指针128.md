---
title: "leetcode-双指针128"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最长的准回文子字符串

给你一个由小写英文字母组成的字符串 s。

如果一个子字符串在删除 恰好 一个字符后变成回文字符串，那么这个子字符串就是 准回文串（almost-palindromic）。

返回一个整数，表示字符串 s 中最长的 准回文串 的长度。

子字符串是字符串中任意连续的、非空 字符序列。

回文串是一个 非空 字符串，正着读和反着读都相同。


```
impl Solution {
    pub fn almost_palindromic(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();
        let mut ans = 0usize;

        // 从 (l, r) 向两边扩展，返回扩展后回文串的长度
        let expand = |mut l: isize, mut r: isize| -> usize {
            while l >= 0 && r < n as isize && bytes[l as usize] == bytes[r as usize] {
                l -= 1;
                r += 1;
            }
            (r - l - 1) as usize
        };

        // 枚举所有中心（奇数长度和偶数长度共 2n-1 个）
        let mut i = 0;
        while i < 2 * n - 1 && ans < n {
            let mut l = (i / 2) as isize;
            let mut r = ((i + 1) / 2) as isize;

            // 先找到以 i 为中心的最长回文
            while l >= 0 && r < n as isize && bytes[l as usize] == bytes[r as usize] {
                l -= 1;
                r += 1;
            }

            // 尝试删除 s[l]（即跳过左边一个字符），继续扩展
            ans = ans.max(expand(l - 1, r));
            // 尝试删除 s[r]（即跳过右边一个字符），继续扩展
            ans = ans.max(expand(l, r + 1));

            i += 1;
        }

        ans.min(n) as i32
    }
}
```
