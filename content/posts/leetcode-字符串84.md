---
title: "leetcode-字符串84"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 统计有效前缀数目

给你一个 二进制 字符串 s。

如果 s 的某个 前缀 的字符可以重新排列成一个 交替 字符串，那么该前缀被认为是 有效 的。

返回 s 中有效前缀的数量。

二进制 字符串是仅由 '0' 和 '1' 组成的字符串。

字符串的 前缀 是指从字符串的开头开始并延伸到其内任意点的 子字符串。

子字符串 是字符串中连续且 非空 的字符序列。

如果一个字符串中没有两个相邻字符相等，那么它被认为是 交替 的。


```
impl Solution {
    pub fn count_valid_prefixes(s: String) -> i32 {
        // 前缀能排成交替串的充要条件：0 和 1 的数量差不超过 1
        let mut diff = 0i32;
        let mut ans = 0;
        for b in s.bytes() {
            diff += if b == b'0' { 1 } else { -1 };
            if diff.abs() <= 1 {
                ans += 1;
            }
        }
        ans
    }
}
```
