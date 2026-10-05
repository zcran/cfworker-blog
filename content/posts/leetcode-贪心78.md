---
title: "leetcode-贪心78"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 字符串中的最大奇数

给你一个字符串 num ，表示一个大整数。请你在字符串 num 的所有 非空子字符串 中找出 值最大的奇数 ，并以字符串形式返回。如果不存在奇数，则返回一个空字符串 "" 。

子字符串 是字符串中的一个连续的字符序列。


```
impl Solution {
    pub fn largest_odd_number(num: String) -> String {
        // 从末尾去掉所有偶数数字，剩下的末尾即为最靠右的奇数位
        // 该前缀以奇数结尾，是值最大的奇子串
        num.trim_end_matches(|c| (c as u8 - b'0') % 2 == 0).to_string()
    }
}
```
