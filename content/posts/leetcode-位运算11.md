---
title: "leetcode-位运算11"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 找不同

给定两个字符串 s 和 t ，它们只包含小写字母。

字符串 t 由字符串 s 随机重排，然后在随机位置添加一个字母。

请找出在 t 中被添加的字母。


```
impl Solution {
    /// 核心思路（异或）：
    /// 将 s 和 t 中所有字符的字节一起异或，
    /// 成对出现的字符抵消为 0，最终剩下的就是 t 中多出的那个字符。
    ///
    /// 利用 `char::from_u32` 将异或结果转回字符，安全且无需 unsafe。
    pub fn find_the_difference(s: String, t: String) -> char {
        let diff = s
            .bytes()
            .chain(t.bytes())
            .fold(0u8, |acc, b| acc ^ b);

        diff as char
    }
}
```
