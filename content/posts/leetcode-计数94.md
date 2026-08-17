---
title: "leetcode-计数94"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 替换字符串中的问号使分数最小

给你一个字符串 s 。s[i] 要么是小写英文字母，要么是问号 '?' 。

对于长度为 m 且 只 含有小写英文字母的字符串 t ，我们定义函数 cost(i) 为下标 i 之前（也就是范围 [0, i - 1] 中）出现过与 t[i] 相同 字符出现的次数。

字符串 t 的 分数 为所有下标 i 的 cost(i) 之 和 。

比方说，字符串 t = "aab" ：

· cost(0) = 0
· cost(1) = 1
· cost(2) = 0
· 所以，字符串 "aab" 的分数为 0 + 1 + 0 = 1 。

你的任务是用小写英文字母 替换 s 中 所有 问号，使 s 的 分数最小 。

请你返回替换所有问号 '?' 之后且分数最小的字符串。如果有多个字符串的 分数最小 ，那么返回字典序最小的一个。




```
impl Solution {
    pub fn minimize_string_value(mut s: String) -> String {
        let bytes = unsafe { s.as_bytes_mut() };
        let mut freq = [0; 26];
        let mut q = 0;

        for &b in bytes.iter() {
            if b == b'?' {
                q += 1;
            } else {
                freq[(b - b'a') as usize] += 1;
            }
        }

        // 按(频率, 字母)排序，频率相同则按字母升序（字典序小优先）
        let mut items: Vec<(i32, u8)> = (0..26)
            .map(|i| (freq[i], b'a' + i as u8))
            .collect();

        let mut chosen = Vec::with_capacity(q);
        for _ in 0..q {
            items.sort_by(|a, b| a.0.cmp(&b.0).then(a.1.cmp(&b.1)));
            let (f, c) = items[0];
            chosen.push(c);
            items[0] = (f + 1, c);
        }

        chosen.sort_unstable();

        let mut j = 0;
        for b in bytes.iter_mut() {
            if *b == b'?' {
                *b = chosen[j];
                j += 1;
            }
        }

        s
    }
}
```
