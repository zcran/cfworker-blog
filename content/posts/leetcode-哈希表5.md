---
title: "leetcode-哈希表5"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 同构字符串

给定两个字符串 s 和 t ，判断它们是否是同构的。

如果 s 中的字符可以按某种映射关系替换得到 t ，那么这两个字符串是同构的。

每个出现的字符都应当映射到另一个字符，同时不改变字符的顺序。不同字符不能映射到同一个字符上，相同字符只能映射到同一个字符上，字符可以映射到自己本身。


```
impl Solution {
    pub fn is_isomorphic(s: String, t: String) -> bool {
        // 同构要求字符间是双射：s→t 与 t→s 都必须是单值映射。
        // 用两个数组代替 HashMap，ASCII 字符共 256 种，省去哈希开销。
        let (s, t) = (s.as_bytes(), t.as_bytes());
        if s.len() != t.len() {
            return false;
        }

        let mut s2t = [0u8; 256];
        let mut t2s = [0u8; 256];
        for i in 0..s.len() {
            let (x, y) = (s[i], t[i]);
            // 0 表示未映射；非 0 时须与已记录的映射一致。
            if s2t[x as usize] == 0 && t2s[y as usize] == 0 {
                s2t[x as usize] = y;
                t2s[y as usize] = x;
            } else if s2t[x as usize] != y || t2s[y as usize] != x {
                return false;
            }
        }
        true
    }
}
```
