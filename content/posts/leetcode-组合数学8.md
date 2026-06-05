---
title: "leetcode-组合数学8"
date: 2026-05-24T09:54:12+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 统计字典序元音字符串的数目


给你一个整数 n，请返回长度为 n 、仅由元音 (a, e, i, o, u) 组成且按 字典序排列 的字符串数量。

字符串 s 按 字典序排列 需要满足：对于所有有效的 i，s[i] 在字母表中的位置总是与 s[i+1] 相同或在 s[i+1] 之前。


```
impl Solution {
    pub fn count_vowel_strings(n: i32) -> i32 {
        (n + 4) * (n + 3) * (n + 2) * (n + 1) / 24
    }
}
```
