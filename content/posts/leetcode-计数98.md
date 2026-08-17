---
title: "leetcode-计数98"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 同位字符串连接的最小长度

给你一个字符串 s ，它由某个字符串 t 和若干 t  的 同位字符串 连接而成。

请你返回字符串 t 的 最小 可能长度。

同位字符串 指的是重新排列一个字符串的字母得到的另外一个字符串。例如，"aab"，"aba" 和 "baa" 是 "aab" 的同位字符串。


```
impl Solution {
    pub fn min_anagram_length(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();

        // 检查长度 m 是否可行：每段 m 个字符的频率是否相同
        let check = |m: usize| -> bool {
            let mut base = [0; 26];
            // 统计第一段频率作为基准
            for k in 0..m {
                base[(bytes[k] - b'a') as usize] += 1;
            }
            // 检查后续每段是否与基准相同
            for j in (m..n).step_by(m) {
                let mut cur = [0; 26];
                for k in j..j + m {
                    cur[(bytes[k] - b'a') as usize] += 1;
                }
                if cur != base {
                    return false;
                }
            }
            true
        };

        // 枚举 t 的长度，从小到大找最小可行解
        for i in 1..=n {
            if n % i == 0 && check(i) {
                return i as i32;
            }
        }
        n as i32
    }
}
```
