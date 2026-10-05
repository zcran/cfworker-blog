---
title: "leetcode-双指针43"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 划分字母区间

给你一个字符串 s 。我们要把这个字符串划分为尽可能多的片段，同一字母最多出现在一个片段中。例如，字符串 "ababcc" 能够被分为 ["abab", "cc"]，但类似 ["aba", "bcc"] 或 ["ab", "ab", "cc"] 的划分是非法的。

注意，划分结果需要满足：将所有划分结果按顺序连接，得到的字符串仍然是 s 。

返回一个表示每个字符串片段的长度的列表。


```
impl Solution {
    pub fn partition_labels(s: String) -> Vec<i32> {
        // 记录每个字符最后一次出现的位置
        let mut last_pos = [0; 26];
        for (i, ch) in s.bytes().enumerate() {
            last_pos[(ch - b'a') as usize] = i;
        }

        let mut result = Vec::new();
        let mut start = 0;
        let mut end = 0;

        // 遍历字符串，动态扩展当前片段的右边界
        for (i, ch) in s.bytes().enumerate() {
            let idx = (ch - b'a') as usize;
            end = end.max(last_pos[idx]);

            // 当前位置达到片段右边界，完成一个片段
            if i == end {
                result.push((end - start + 1) as i32);
                start = i + 1;
            }
        }

        result
    }
}
```
