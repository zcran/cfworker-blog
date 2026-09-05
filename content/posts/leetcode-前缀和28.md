---
title: "leetcode-前缀和28"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 字符串的好分割数目

给你一个字符串 s ，一个分割被称为 「好分割」 当它满足：将 s 分割成 2 个字符串 p 和 q ，它们连接起来等于 s 且 p 和 q 中不同字符的数目相同。

请你返回 s 中好分割的数目。


```
impl Solution {
    pub fn num_splits(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();

        // 右侧不同字符计数
        let mut right_cnt = [0; 26];
        let mut right_unique = 0;
        for &ch in s {
            let idx = (ch - b'a') as usize;
            if right_cnt[idx] == 0 {
                right_unique += 1;
            }
            right_cnt[idx] += 1;
        }

        // 左侧不同字符计数，同时滑动分割线
        let mut left_cnt = [0; 26];
        let mut left_unique = 0;
        let mut result = 0;

        // 分割线在 i 之后，左子串为 s[0..=i]，右子串为 s[i+1..]
        // 左子串不能为空，所以 i 从 0 到 n-2
        for i in 0..n - 1 {
            let idx = (s[i] - b'a') as usize;

            // 将当前字符加入左侧
            if left_cnt[idx] == 0 {
                left_unique += 1;
            }
            left_cnt[idx] += 1;

            // 从右侧移除当前字符
            right_cnt[idx] -= 1;
            if right_cnt[idx] == 0 {
                right_unique -= 1;
            }

            if left_unique == right_unique {
                result += 1;
            }
        }

        result
    }
}
```
