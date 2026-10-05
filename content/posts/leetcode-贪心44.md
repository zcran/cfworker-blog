---
title: "leetcode-贪心44"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 6 和 9 组成的最大数字

给你一个仅由数字 6 和 9 组成的正整数 num。

你最多只能翻转一位数字，将 6 变成 9，或者把 9 变成 6 。

请返回你可以得到的最大数字。


```
impl Solution {
    pub fn maximum69_number(num: i32) -> i32 {
        // 从左往右找到第一个 '6' 翻成 '9'，即可得到最大数字
        let s = num.to_string();
        match s.find('6') {
            Some(i) => {
                let mut bytes = s.into_bytes();
                bytes[i] = b'9';
                // 全部为 ASCII 数字，转换必定安全
                String::from_utf8(bytes).unwrap().parse().unwrap()
            }
            None => num,
        }
    }
}
```
