---
title: "leetcode-滑动窗口102"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 字符至少出现 K 次的子字符串 I

给你一个字符串 s 和一个整数 k，在 s 的所有子字符串中，请你统计并返回 至少有一个 字符 至少出现 k 次的子字符串总数。

子字符串 是字符串中的一个连续、 非空 的字符序列。


```
impl Solution {
    pub fn number_of_substrings(s: String, k: i32) -> i32 {
        let s = s.as_bytes();
        let k = k as usize;
        let mut freq = [0; 26];
        let mut left = 0;
        let mut ans = 0;

        for &ch in s {
            let idx = (ch - b'a') as usize;
            freq[idx] += 1;

            // 当当前字符出现次数达到 k 时，收缩左指针
            while freq[idx] >= k {
                let left_idx = (s[left] - b'a') as usize;
                freq[left_idx] -= 1;
                left += 1;
            }

            // 以当前右端点结尾的所有合法子串数量
            // 左端点可以是 0..=left-1，共 left 个
            ans += left;
        }

        ans as i32
    }
}
```
