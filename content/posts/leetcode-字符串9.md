---
title: "leetcode-字符串9"
date: 2026-10-10T12:56:51+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 密钥格式化

给定一个许可密钥字符串 s，仅由字母、数字字符和破折号组成。字符串由 n 个破折号分成 n + 1 组。你也会得到一个整数 k 。

我们想要重新格式化字符串 s，使每一组包含 k 个字符，除了第一组，它可以比 k 短，但仍然必须包含至少一个字符。此外，两组之间必须插入破折号，并且应该将所有小写字母转换为大写字母。

返回 重新格式化的许可密钥 。


```
impl Solution {
    pub fn license_key_formatting(s: String, k: i32) -> String {
        let k = k as usize;
        // 先过滤破折号并转大写，得到纯字符序列
        let chars: Vec<u8> = s
            .bytes()
            .filter(|&b| b != b'-')
            .map(|b| b.to_ascii_uppercase())
            .collect();

        let n = chars.len();
        if n == 0 {
            return String::new();
        }

        // 第一组长度：n % k（为 0 时用 k，保证每组恰好 k 个）
        let first = if n % k == 0 { k } else { n % k };
        // 预分配：字符数 + 组间破折号数（n >= 1 时 (n-1)/k 安全）
        let mut result = String::with_capacity(n + (n - 1) / k);

        result.push_str(std::str::from_utf8(&chars[..first]).unwrap());
        let mut i = first;
        while i < n {
            result.push('-');
            result.push_str(std::str::from_utf8(&chars[i..i + k]).unwrap());
            i += k;
        }

        result
    }
}
```
