---
title: "leetcode-位运算85"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 判断 ASCII 值回文

给你一个由小写英文字母组成的字符串 s。

将 s 中的每个字符替换为其 ASCII 值对应的 8 位二进制表示，包括前导零，并保持字符原有顺序，从而构造一个二进制字符串。

如果得到的二进制字符串是一个 回文串 ，则返回 true；否则返回 false。

二进制字符串 是指仅由字符 '0' 和 '1' 组成的字符串。

回文串 是指正着读和反着读都相同的字符串。


```
impl Solution {
    pub fn is_palindromic(s: String) -> bool {
        // 思路：把每个字符看作 8 位二进制串。
        // 整个二进制串是回文，等价于：
        //   第 i 个字符的 8 位二进制 == 第 n-1-i 个字符的 8 位二进制的反转
        // 即：reverse_bits8(s[i]) == s[n-1-i]
        //
        // 注意：原 C++ 代码循环到 n/2（含），当 n 为奇数时会检查中间字符自身，
        // 要求 reverse_bits8(中间字符) == 中间字符，即中间字符的 8 位表示本身是回文。

        let bytes = s.as_bytes();
        let n = bytes.len();

        for i in 0..=n / 2 {
            if bytes[i].reverse_bits() != bytes[n - 1 - i] {
                return false;
            }
        }
        true
    }
}
```
