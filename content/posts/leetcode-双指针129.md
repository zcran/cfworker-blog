---
title: "leetcode-双指针129"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 双端字符匹配

给你一个长度为 n 的字符串 s，其中只包含小写英文字母。

返回最小的下标 i，使得 s[i] == s[n - i - 1]。

如果不存在这样的下标，返回 -1。


```
impl Solution {
    pub fn first_matching_index(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();
        // 只需检查前一半（含中间位置）
        for i in 0..=n / 2 {
            if bytes[i] == bytes[n - 1 - i] {
                return i as i32;
            }
        }
        -1
    }
}
```
