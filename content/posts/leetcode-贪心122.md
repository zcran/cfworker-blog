---
title: "leetcode-贪心122"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 执行子串操作后的字典序最小字符串

给你一个仅由小写英文字母组成的字符串 s 。在一步操作中，你可以完成以下行为：

选择 s 的任一非空子字符串，可能是整个字符串，接着将字符串中的每一个字符替换为英文字母表中的前一个字符。例如，'b' 用 'a' 替换，'a' 用 'z' 替换。

返回执行上述操作 恰好一次 后可以获得的 字典序最小 的字符串。

子字符串 是字符串中的一个连续字符序列。

现有长度相同的两个字符串 x 和 字符串 y ，在满足 x[i] != y[i] 的第一个位置 i 上，如果  x[i] 在字母表中先于 y[i] 出现，则认为字符串 x 比字符串 y 字典序更小 。


```
impl Solution {
    pub fn smallest_string(s: String) -> String {
        // 贪心：找到第一个不是 'a' 的字符，从它开始逐个减 1，
        // 直到遇到下一个 'a' 或字符串结束。
        // 若全是 'a'，则只能把最后一个 'a' 变成 'z'。
        let mut bytes = s.into_bytes();
        let n = bytes.len();

        let start = match bytes.iter().position(|&b| b != b'a') {
            Some(i) => i,
            None => {
                // 全是 'a'，把最后一个改为 'z'
                bytes[n - 1] = b'z';
                return String::from_utf8(bytes).unwrap();
            }
        };

        for b in bytes[start..].iter_mut() {
            if *b == b'a' {
                break;
            }
            *b -= 1;
        }

        String::from_utf8(bytes).unwrap()
    }
}
```
