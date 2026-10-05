---
title: "leetcode-双指针93"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 追加字符以获得子序列

给你两个仅由小写英文字母组成的字符串 s 和 t 。

现在需要通过向 s 末尾追加字符的方式使 t 变成 s 的一个 子序列 ，返回需要追加的最少字符数。

子序列是一个可以由其他字符串删除部分（或不删除）字符但不改变剩下字符顺序得到的字符串。


```
impl Solution {
    pub fn append_characters(s: String, t: String) -> i32 {
        let (s, t) = (s.as_bytes(), t.as_bytes());
        let mut j = 0; // t 中已匹配的字符数

        // 在 s 中按顺序匹配 t 的前缀
        for &c in s {
            if j < t.len() && c == t[j] {
                j += 1;
            }
        }

        // t 中剩余未匹配的字符数即为需要追加的最少字符数
        (t.len() - j) as i32
    }
}
```
