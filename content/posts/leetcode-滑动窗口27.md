---
title: "leetcode-滑动窗口27"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 单字符重复子串的最大长度

如果字符串中的所有字符都相同，那么这个字符串是单字符重复的字符串。

给你一个字符串 text，你只能交换其中两个字符一次或者什么都不做，然后得到一些单字符重复的子串。返回其中最长的子串的长度。


```
impl Solution {
    pub fn max_rep_opt1(text: String) -> i32 {
        let chars: Vec<char> = text.chars().collect();
        let n = chars.len();

        // 统计每个字符的总出现次数
        let mut total_count = [0; 26];
        for &c in &chars {
            total_count[(c as u8 - b'a') as usize] += 1;
        }

        let mut max_len = 0;
        let mut i = 0;

        // 对每个起始位置，尝试扩展单字符重复子串
        while i < n {
            let c = chars[i];
            let idx = (c as u8 - b'a') as usize;
            let mut j = i;

            // 统计连续相同字符的长度
            while j < n && chars[j] == c {
                j += 1;
            }
            let len = j - i;

            // 如果该字符总数大于 len，可以交换一个其他字符进来
            if total_count[idx] > len {
                // 检查后面是否隔一个字符又有相同字符（中间隔了一个不同字符）
                if j + 1 < n && chars[j + 1] == c {
                    // 两个相同块中间只隔一个字符，可以合并
                    let mut k = j + 1;
                    while k < n && chars[k] == c {
                        k += 1;
                    }
                    let second_len = k - (j + 1);
                    // 如果能从其他地方再借一个字符，可以再加1
                    let can_borrow = if total_count[idx] > len + second_len { 1 } else { 0 };
                    max_len = max_len.max(len + second_len + can_borrow);
                } else {
                    // 普通情况：从其他地方借一个字符
                    max_len = max_len.max(len + 1);
                }
            } else {
                // 所有该字符都在此连续块中
                max_len = max_len.max(len);
            }

            // 跳过已检查的连续块
            i = j;
        }

        max_len as i32
    }
}
```
