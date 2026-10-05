---
title: "leetcode-位运算92"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 判定字符是否唯一

实现一个算法，确定一个字符串 s 的所有字符是否全都不同。

示例 1：

输入: s = "leetcode"
输出: false
示例 2：

输入: s = "abc"
输出: true
限制：

0 <= len(s) <= 100
s[i]仅包含小写字母
如果你不使用额外的数据结构，会很加分。

```
impl Solution {
    pub fn is_unique(astr: String) -> bool {
        // 仅含小写字母，用 32 位整数的位图记录出现过的字符。
        // 超过 26 个字符必有重复。
        let bytes = astr.as_bytes();
        if bytes.len() > 26 {
            return false;
        }

        let mut mask = 0u32;
        for &b in bytes {
            let bit = 1u32 << (b - b'a');
            // 若该位已置 1，说明字符重复
            if mask & bit != 0 {
                return false;
            }
            mask |= bit;
        }
        true
    }
}
```
