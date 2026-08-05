---
title: "leetcode-计数58"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 构造限制重复的字符串

给你一个字符串 s 和一个整数 repeatLimit ，用 s 中的字符构造一个新字符串 repeatLimitedString ，使任何字母 连续 出现的次数都不超过 repeatLimit 次。你不必使用 s 中的全部字符。

返回 字典序最大的 repeatLimitedString 。

如果在字符串 a 和 b 不同的第一个位置，字符串 a 中的字母在字母表中出现时间比字符串 b 对应的字母晚，则认为字符串 a 比字符串 b 字典序更大 。如果字符串中前 min(a.length, b.length) 个字符都相同，那么较长的字符串字典序更大。


```
impl Solution {
    pub fn repeat_limited_string(s: String, repeat_limit: i32) -> String {
        // 统计每个字母的出现次数
        let mut cnt = vec![0; 26];
        s.bytes().for_each(|c| {
            cnt[(c - b'a') as usize] += 1;
        });

        let mut ans = String::new();

        // i: 当前要使用的最大字母索引
        // j: 用于插入间隔的次大字母索引
        let (mut i, mut j) = (25, 24);
        // 当前最大字母已连续使用的次数
        let mut repeat = 0;

        while i < 26 && j < 25 {
            if cnt[i] == 0 {
                // 当前最大字母已用完，移动到下一个字母
                repeat = 0;
                i -= 1;
            } else if repeat < repeat_limit {
                // 还可以继续使用当前最大字母
                ans.push((b'a' + i as u8) as char);
                cnt[i] -= 1;
                repeat += 1;
            } else if j >= i || cnt[j] == 0 {
                // 次大字母位置无效或已用完，继续向前寻找
                j -= 1;
            } else {
                // 插入次大字母作为间隔，重置连续计数
                ans.push((b'a' + j as u8) as char);
                cnt[j] -= 1;
                repeat = 0;
            }
        }

        ans
    }
}
```
