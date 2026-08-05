---
title: "leetcode-枚举88"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 反转后字典序最小的字符串

给你一个由小写英文字母组成的、长度为 n 的字符串 s。

你 必须执行 恰好 一次操作：选择一个整数 k，满足 1 <= k <= n，然后执行以下两个选项之一：

反转 s 的 前 k 个字符，或
反转 s 的 后 k 个字符。

返回在 恰好 执行一次此类操作后可以获得的 字典序最小 的字符串。

如果字符串 a 和字符串 b 在第一个不同的位置上，a 中的字母在字母表中比 b 中对应的字母出现得更早，则称字符串 a 字典序小于 字符串 b。如果前 min(a.length, b.length) 个字符都相同，则较短的字符串字典序较小。


```
impl Solution {
    pub fn lex_smallest(s: String) -> String {
        let bytes = s.as_bytes();
        let n = bytes.len();

        // k = 1 时反转不改变字符串，作为初始答案
        let mut ans = s.clone();

        // 枚举所有可能的 k (2..=n)
        for k in 2..=n {
            // 情况1：反转前 k 个字符
            // 构造: reverse(s[0..k]) + s[k..n]
            let mut prefix = bytes[..k].to_vec();
            prefix.reverse();
            let candidate1 = String::from_utf8(
                [prefix, bytes[k..].to_vec()].concat()
            ).unwrap();
            if candidate1 < ans {
                ans = candidate1;
            }

            // 情况2：反转后 k 个字符
            // 构造: s[0..n-k] + reverse(s[n-k..n])
            let start = n - k;
            let mut suffix = bytes[start..].to_vec();
            suffix.reverse();
            let candidate2 = String::from_utf8(
                [bytes[..start].to_vec(), suffix].concat()
            ).unwrap();
            if candidate2 < ans {
                ans = candidate2;
            }
        }

        ans
    }
}
```
