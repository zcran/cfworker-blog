---
title: "leetcode-哈希表66"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 字符串及其反转中是否存在同一子字符串

给你一个字符串 s ，请你判断字符串 s 是否存在一个长度为 2 的子字符串，在 s 反转后的字符串中也出现。

如果存在这样的子字符串，返回 true；如果不存在，返回 false 。


```
impl Solution {
    pub fn is_substring_present(s: String) -> bool {
        let b = s.as_bytes();

        // 记录原串中所有长度为 2 的子串（用 26 位掩码表示 x->y）
        let mut seen = [0u32; 26];
        for w in b.windows(2) {
            let x = (w[0] - b'a') as usize;
            let y = (w[1] - b'a') as usize;
            seen[x] |= 1 << y;
        }

        // 检查每个长度为 2 的子串的反转是否也出现在原串中
        for w in b.windows(2) {
            let x = (w[0] - b'a') as usize;
            let y = (w[1] - b'a') as usize;
            // 反转后为 y -> x，即查询 seen[y] 的第 x 位
            if seen[y] >> x & 1 == 1 {
                return true;
            }
        }

        false
    }
}
```
