---
title: "leetcode-滚动哈希7"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 检查一个字符串是否包含所有长度为 K 的二进制子串

给你一个二进制字符串 s 和一个整数 k 。如果所有长度为 k 的二进制字符串都是 s 的子串，请返回 true ，否则请返回 false 。

```
impl Solution {
    /// 检查二进制字符串 `s` 是否包含所有长度为 `k` 的二进制子串。
    ///
    /// # 原理
    /// - 长度为 k 的二进制子串共有 `2^k` 种可能（从 0 到 2^k - 1）。
    /// - 使用滑动窗口 + 位掩码将每个子串编码为一个整数，记录出现过的编码。
    /// - 若所有编码都出现过，则返回 true。
    ///
    /// # 参数
    /// - `s`: 仅含 '0' 和 '1' 的字符串
    /// - `k`: 子串长度
    ///
    /// # 返回值
    /// - 是否包含所有可能的长度为 k 的二进制子串
    pub fn has_all_codes(s: String, k: i32) -> bool {
        let k = k as usize;
        let bytes = s.as_bytes();
        let n = bytes.len();

        // 长度不足，不可能包含所有子串
        if n < k {
            return false;
        }

        let total_codes = 1 << k;          // 2^k 种可能
        let mask = total_codes - 1;        // 低 k 位全 1，用于保留窗口值

        // 记录每种编码是否出现过
        let mut seen = vec![false; total_codes];

        // 计算第一个窗口的编码值
        let first_window = bytes[..k]
            .iter()
            .fold(0, |acc, &b| (acc << 1) | ((b - b'0') as usize));

        seen[first_window] = true;
        let mut found_count = 1;

        // 滑动窗口：从第 k 个字符开始，滚动更新编码
        bytes[k..]
            .iter()
            .fold(first_window, |curr, &b| {
                let next = ((curr << 1) & mask) | ((b - b'0') as usize);
                if !seen[next] {
                    seen[next] = true;
                    found_count += 1;
                }
                next
            });

        found_count == total_codes
    }
}
```
