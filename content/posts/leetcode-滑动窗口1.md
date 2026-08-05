---
title: "leetcode-滑动窗口1"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 无重复字符的最长子串

给定一个字符串 s ，请你找出其中不含有重复字符的 最长 子串 的长度。


```
use std::collections::HashMap;

impl Solution {
    /// 计算最长不重复子串的长度
    ///
    /// 使用滑动窗口 + HashMap 记录每个字符最后出现的位置
    /// 时间复杂度: O(n), 空间复杂度: O(min(n, 字符集大小))
    pub fn length_of_longest_substring(s: String) -> i32 {
        let mut last_pos = HashMap::new();  // 字符 -> 最近一次出现的索引
        let mut left = 0;                  // 窗口左边界
        let mut max_len = 0;               // 最大长度

        for (right, ch) in s.chars().enumerate() {
            // 如果当前字符已在窗口中, 将左边界移动到重复字符之后
            if let Some(&prev) = last_pos.get(&ch) {
                left = left.max(prev + 1);
            }

            // 更新当前字符的最新位置
            last_pos.insert(ch, right);

            // 更新最大长度 (窗口大小 = right - left + 1)
            max_len = max_len.max(right - left + 1);
        }

        max_len as i32
    }
}
```
