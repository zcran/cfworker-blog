---
title: "leetcode-滑动窗口31"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 子串的最大出现次数

给你一个字符串 s ，请你返回满足以下条件且出现次数最大的 任意 子串的出现次数：

子串中不同字母的数目必须小于等于 maxLetters 。
子串的长度必须大于等于 minSize 且小于等于 maxSize 。


```
impl Solution {
    pub fn max_freq(s: String, max_letters: i32, min_size: i32, _max_size: i32) -> i32 {
        let s = s.as_bytes();
        let min_size = min_size as usize;
        let mut freq = std::collections::HashMap::new();
        let mut char_count = [0; 26];
        let mut unique = 0;
        let mut ans = 0;

        // 只考虑长度为 min_size 的子串即可
        // 因为如果长度为 L > min_size 的子串满足条件，其长度为 min_size 的子串也满足
        for right in 0..s.len() {
            // 加入右边界字符
            let in_idx = (s[right] - b'a') as usize;
            if char_count[in_idx] == 0 {
                unique += 1;
            }
            char_count[in_idx] += 1;

            // 窗口长度达到 min_size 时才处理
            if right + 1 >= min_size {
                let left = right + 1 - min_size;

                // 如果不同字符数符合要求，记录子串出现次数
                if unique <= max_letters {
                    let sub = &s[left..=right];
                    let count = freq.entry(sub).or_insert(0);
                    *count += 1;
                    ans = ans.max(*count);
                }

                // 移除左边界字符，为下一轮准备
                let out_idx = (s[left] - b'a') as usize;
                char_count[out_idx] -= 1;
                if char_count[out_idx] == 0 {
                    unique -= 1;
                }
            }
        }

        ans
    }
}
```
