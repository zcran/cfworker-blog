---
title: "leetcode-前缀和23"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 分割字符串的最大得分

给你一个由若干 0 和 1 组成的字符串 s ，请你计算并返回将该字符串分割成两个 非空 子字符串（即 左 子字符串和 右 子字符串）所能获得的最大得分。

「分割字符串的得分」为 左 子字符串中 0 的数量加上 右 子字符串中 1 的数量。


```
impl Solution {
    pub fn max_score(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();

        // 统计右侧 1 的总数
        let total_ones = s.iter().filter(|&&c| c == b'1').count() as i32;

        let mut left_zeros = 0;
        let mut right_ones = total_ones;
        let mut max_score = 0;

        // 分割线在 i 之后，左子串为 s[0..=i]，右子串为 s[i+1..]
        // 左子串必须非空，所以分割线最多到 n-2
        for i in 0..n - 1 {
            if s[i] == b'0' {
                left_zeros += 1;
            } else {
                right_ones -= 1;
            }
            let score = left_zeros + right_ones;
            if score > max_score {
                max_score = score;
            }
        }

        max_score
    }
}
```
