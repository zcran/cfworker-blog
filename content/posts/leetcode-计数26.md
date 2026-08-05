---
title: "leetcode-计数26"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 构造 K 个回文字符串

给你一个字符串 s 和一个整数 k 。请你用 s 字符串中 所有字符 构造 k 个非空 回文串 。

如果你可以用 s 中所有字符构造 k 个回文字符串，那么请你返回 True ，否则返回 False 。


```
impl Solution {
    pub fn can_construct(s: String, k: i32) -> bool {
        let n = s.len();

        // 如果 k 大于字符串长度，无法构造 k 个非空回文串
        if k as usize > n {
            return false;
        }

        // 统计每个字母出现次数的奇偶性
        let mut odd_count = 0;
        let mut freq = [0; 26];

        for ch in s.bytes() {
            let idx = (ch - b'a') as usize;
            freq[idx] += 1;
            // 每出现一次，翻转奇偶状态
            if freq[idx] % 2 == 1 {
                odd_count += 1;
            } else {
                odd_count -= 1;
            }
        }

        // 奇数个字符的数量必须 <= k
        // 因为每个回文串最多只能包含一个奇数频率的字符
        odd_count <= k
    }
}
```
