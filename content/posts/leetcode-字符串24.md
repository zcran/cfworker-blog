---
title: "leetcode-字符串24"
date: 2026-10-10T12:56:52+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 连续字符

给你一个字符串 s ，字符串的「能量」定义为：只包含一种字符的最长非空子字符串的长度。

请你返回字符串 s 的 能量。


```
impl Solution {
    pub fn max_power(s: String) -> i32 {
        // 一次遍历：cur 记录当前连续相同字符的长度，ans 记录最大值
        let (mut ans, mut cur) = (1, 1);
        let bytes = s.as_bytes();

        for i in 1..bytes.len() {
            if bytes[i] == bytes[i - 1] {
                cur += 1;
                ans = ans.max(cur);
            } else {
                cur = 1;
            }
        }

        ans
    }
}
```
