---
title: "leetcode-双指针34"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 反转字符串 II

给定一个字符串 s 和一个整数 k，从字符串开头算起，每计数至 2k 个字符，就反转这 2k 字符中的前 k 个字符，再重新计数。

如果剩余字符少于 k 个，则将剩余字符全部反转。

如果剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符，其余字符保持原样。


```
impl Solution {
    pub fn reverse_str(s: String, k: i32) -> String {
        let mut bytes = s.into_bytes();
        let n = bytes.len();
        let k = k as usize;

        // 每 2k 个字符为一组，反转前 k 个
        for start in (0..n).step_by(2 * k) {
            let end = (start + k).min(n);
            bytes[start..end].reverse();
        }

        String::from_utf8(bytes).unwrap()
    }
}
```
