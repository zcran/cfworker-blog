---
title: "leetcode-计数68"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 重排字符形成目标字符串

给你两个下标从 0 开始的字符串 s 和 target 。你可以从 s 取出一些字符并将其重排，得到若干新的字符串。

从 s 中取出字符并重新排列，返回可以形成 target 的 最大 副本数。


```
impl Solution {
    pub fn rearrange_characters(s: String, target: String) -> i32 {
        // 统计两个字符串中每个字母的出现次数
        let mut cnt_s = [0; 26];
        let mut cnt_t = [0; 26];

        for b in s.bytes() {
            cnt_s[(b - b'a') as usize] += 1;
        }
        for b in target.bytes() {
            cnt_t[(b - b'a') as usize] += 1;
        }

        // 计算 target 中的每个字母在 s 中最多能组成多少个副本
        // 取所有字母的最小值，即为最大副本数
        let mut max_copies = i32::MAX;
        for i in 0..26 {
            if cnt_t[i] > 0 {
                let copies = cnt_s[i] / cnt_t[i];
                max_copies = max_copies.min(copies);
            }
        }

        if max_copies == i32::MAX { 0 } else { max_copies }
    }
}
```
