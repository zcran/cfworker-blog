---
title: "leetcode-滑动窗口23"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 子串能表示从 1 到 N 数字的二进制串

给定一个二进制字符串 s 和一个正整数 n，如果对于 [1, n] 范围内的每个整数，其二进制表示都是 s 的 子字符串 ，就返回 true，否则返回 false 。

子字符串 是字符串中连续的字符序列。


```
impl Solution {
    pub fn query_string(s: String, n: i32) -> bool {
        // 只需要检查 [n/2+1, n] 范围的数即可
        // 因为 [1, n/2] 的二进制表示可以通过左移得到 [n/2+1, n] 中某个数的前缀
        for i in (n / 2 + 1..=n).rev() {
            if !s.contains(&format!("{:b}", i)) {
                return false;
            }
        }
        true
    }
}
```
