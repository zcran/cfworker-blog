---
title: "leetcode-枚举31"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 兼具大小写的最好英文字母

给你一个由英文字母组成的字符串 s ，请你找出并返回 s 中的 最好 英文字母。返回的字母必须为大写形式。如果不存在满足条件的字母，则返回一个空字符串。

最好 英文字母的大写和小写形式必须 都 在 s 中出现。

英文字母 b 比另一个英文字母 a 更好 的前提是：英文字母表中，b 在 a 之 后 出现。


```
impl Solution {
    pub fn greatest_letter(s: String) -> String {
        // 使用位掩码记录每个字母的出现情况
        // 低26位表示小写字母，高26位表示大写字母
        let mut mask = 0u64;
        for &b in s.as_bytes() {
            if b >= b'a' {
                // 小写字母：设置低26位的对应位
                mask |= 1u64 << (b - b'a');
            } else {
                // 大写字母：设置高26位的对应位
                mask |= 1u64 << (b - b'A' + 26);
            }
        }

        // 从 'Z' 到 'A' 逆序检查
        for i in (0..26).rev() {
            // 检查大写和小写是否同时存在
            // 低26位和第 i 位，高26位和第 i+26 位
            if (mask & (1u64 << i)) != 0 && (mask & (1u64 << (i + 26))) != 0 {
                return char::from(b'A' + i as u8).to_string();
            }
        }

        String::new()
    }
}
```
