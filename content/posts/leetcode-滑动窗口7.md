---
title: "leetcode-滑动窗口7"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 替换后的最长重复字符

给你一个字符串 s 和一个整数 k 。你可以选择字符串中的任一字符，并将其更改为任何其他大写英文字符。该操作最多可执行 k 次。

在执行上述操作后，返回 包含相同字母的最长子字符串的长度。


```
impl Solution {
    pub fn character_replacement(s: String, k: i32) -> i32 {
        let s = s.as_bytes();
        let k = k as usize;
        let mut count = [0; 26];
        let mut max_freq = 0;
        let mut left = 0;
        let mut max_len = 0;

        for (right, &ch) in s.iter().enumerate() {
            let idx = (ch - b'A') as usize;
            count[idx] += 1;
            max_freq = max_freq.max(count[idx]);

            // 当前窗口长度 - 出现最多的字符次数 > k，说明需要替换的字符超过 k 个，收缩左边界
            let window_len = right - left + 1;
            if window_len - max_freq > k {
                count[(s[left] - b'A') as usize] -= 1;
                left += 1;
            }

            // 更新最大长度（窗口长度可能因收缩而变小，但 left 增加时窗口长度不变或变小，无需担心影响 max_len）
            max_len = max_len.max(right - left + 1);
        }

        max_len as i32
    }
}
```
