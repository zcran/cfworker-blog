---
title: "leetcode-排序72"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 最小回文排列 I

给你一个 回文 字符串 s。

返回 s 的按字典序排列的 最小 回文排列。

如果一个字符串从前往后和从后往前读都相同，那么这个字符串是一个 回文 字符串。

排列 是字符串中所有字符的重排。

如果字符串 a 按字典序小于字符串 b，则表示在第一个不同的位置，a 中的字符比 b 中的对应字符在字母表中更靠前。
如果在前 min(a.length, b.length) 个字符中没有区别，则较短的字符串按字典序更小。



```
impl Solution {
    pub fn smallest_palindrome(s: String) -> String {
        let mut bytes = s.into_bytes();
        let len = bytes.len();
        let half = len / 2;

        // 只需排序前半部分，即可得到字典序最小的回文排列
        bytes[..half].sort_unstable();

        // 将前半部分镜像到后半部分
        for i in 0..half {
            bytes[len - 1 - i] = bytes[i];
        }

        // 输入保证是合法回文构造，转换必定成功
        String::from_utf8(bytes).unwrap()
    }
}
```
