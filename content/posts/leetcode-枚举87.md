---
title: "leetcode-枚举87"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 大于目标字符串的最小字典序排列

给你两个长度均为 n 且仅由小写英文字母组成的字符串 s 和 target。

返回 s 的 字典序最小的排列，要求该排列 严格 大于 target。如果 s 不存在任何字典序严格大于 target 的排列，则返回一个空字符串。

如果两个长度相同的字符串 a 和 b 在它们首次出现不同字符的位置上，字符串 a 对应的字母在字母表中出现在 b 对应字母的 后面 ，则字符串 a 字典序严格大于 字符串 b。

排列 是字符串中所有字符的一种重新排列。


```
impl Solution {
    pub fn lex_greater_permutation(s: String, target: String) -> String {
        let s_bytes = s.as_bytes();
        let target_bytes = target.as_bytes();
        let n = s_bytes.len();

        // 统计 s 和 target 的字符频率差
        // balance[i] > 0 表示 s 比 target 多出的字符数
        let mut balance = [0; 26];
        for &ch in s_bytes {
            balance[(ch - b'a') as usize] += 1;
        }
        for &ch in target_bytes {
            balance[(ch - b'a') as usize] -= 1;
        }

        // 如果 s 和 target 字符组成不同，但 s 的字符无法形成更大排列
        // 这里通过贪心从后往前构造
        let mut ans = vec![0u8; n];

        // 从后往前尝试：先让前缀与 target 相同，再在当前位置放更大的字符
        for pos in (0..n).rev() {
            let cur_idx = (target_bytes[pos] - b'a') as usize;

            // 将当前位置的 target 字符放回，因为前缀要与 target 一致
            balance[cur_idx] += 1;

            // 检查前缀是否能与 target 完全匹配（即 balance 不能有负数）
            if balance.iter().any(|&cnt| cnt < 0) {
                continue;
            }

            // 在当前位置尝试放置比 target[pos] 更大的可用字符
            if let Some(greater_idx) = (cur_idx + 1..26).find(|&idx| balance[idx] > 0) {
                // 前缀与 target 一致
                ans[..pos].copy_from_slice(&target_bytes[..pos]);
                ans[pos] = b'a' + greater_idx as u8;
                balance[greater_idx] -= 1;

                // 剩余字符按字典序升序填充（最小化结果）
                let mut write_pos = pos + 1;
                for ch in 0..26 {
                    for _ in 0..balance[ch] {
                        ans[write_pos] = b'a' + ch as u8;
                        write_pos += 1;
                    }
                }

                return String::from_utf8(ans).unwrap();
            }
        }

        // 没有找到更大的排列
        String::new()
    }
}
```
