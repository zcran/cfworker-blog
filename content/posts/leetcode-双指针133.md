---
title: "leetcode-双指针133"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 重新排列字符串以避免字符对

给你一个字符串 s 和两个 不同 的小写英文字母 x 和 y。

重新排列 s 中的字符来构造一个新的字符串 t，使得：

t 是 s 的一个 排列。
在 t 中，所有 y 都必须在所有 x 之前。

返回 任意 一个有效的字符串 t。

排列 是对一个字符串中所有字符的重新排列。


```
impl Solution {
    pub fn rearrange_string(s: String, x: char, y: char) -> String {
        let mut bytes = s.into_bytes();
        // x < y 时降序排列可保证所有 y 在 x 之前；
        // 否则升序排列即可（其余字符顺序不影响要求）。
        if x < y {
            bytes.sort_unstable_by(|a, b| b.cmp(a));
        } else {
            bytes.sort_unstable();
        }
        String::from_utf8(bytes).unwrap()
    }
}
```
