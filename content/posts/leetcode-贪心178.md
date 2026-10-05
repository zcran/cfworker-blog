---
title: "leetcode-贪心178"
date: 2026-10-03T12:55:46+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 变换二进制字符串的最少操作次数

给你两个长度同为 n 的二进制字符串 s1 和 s2 。

你可以对 s1 以任意顺序执行以下操作 任意 次：

选择一个满足 s1[i] 为 '0' 的下标 i ，并将其更改为 '1' 。

选择一个满足 0 <= i < n - 1 且 s1[i] 和 s1[i + 1] 均为 '1' 的下标 i 。将这两个字符都更改为 '0' 。

返回使 s1 等于 s2 所需的 最小 操作次数。如果无法使 s1 等于 s2 ，则返回 -1 。


```
impl Solution {
    pub fn min_operations(s: String, t: String) -> i32 {
        let n = s.len();
        let mut s = s.into_bytes();
        let t = t.into_bytes();

        // 特殊情况：长度为 1 且 s="1"、t="0"，
        // 既无法把 '1' 变 '0'（只能成对清零），也无法靠置位变成 '0'
        if n == 1 && s[0] == b'1' && t[0] == b'0' {
            return -1;
        }

        let mut ans = 0;

        for i in 0..n {
            if s[i] == t[i] {
                continue;
            }
            if s[i] == b'0' {
                // 需要把 '0' 变 '1'，一次操作即可
                ans += 1;
            } else if i + 1 < n && s[i + 1] == b'1' {
                // s[i]='1' 但 t[i]='0'，恰好右边也是 '1'，成对清零，一步搞定
                ans += 1;
                s[i + 1] = b'0';
            } else {
                // 否则需先把右边置 '1' 再成对清零，共两步
                ans += 2;
            }
        }

        ans
    }
}
```
