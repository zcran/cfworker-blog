---
title: "leetcode-滑动窗口86"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 每个字符最多出现两次的最长子字符串

给你一个字符串 s ，请找出满足每个字符最多出现两次的最长子字符串，并返回该子字符串的 最大 长度。


```
impl Solution {
    pub fn maximum_length_substring(s: String) -> i32 {
        let bytes = s.as_bytes();
        let mut freq = [0; 128]; // ASCII 字符频率表
        let mut left = 0;
        let mut max_len = 0;

        for (right, &ch) in bytes.iter().enumerate() {
            // 1. 扩展窗口：添加右端字符
            freq[ch as usize] += 1;

            // 2. 收缩窗口：当某个字符出现超过 2 次时
            while freq[ch as usize] > 2 {
                freq[bytes[left] as usize] -= 1;
                left += 1;
            }

            // 3. 更新最大长度
            max_len = max_len.max(right - left + 1);
        }

        max_len as i32
    }
}
```
