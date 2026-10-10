---
title: "leetcode-字符串39"
date: 2026-10-10T12:56:54+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 哪种连续子字符串更长

给你一个二进制字符串 s 。如果字符串中由 1 组成的 最长 连续子字符串 严格长于 由 0 组成的 最长 连续子字符串，返回 true ；否则，返回 false 。

例如，s = "110100010" 中，由 1 组成的最长连续子字符串的长度是 2 ，由 0 组成的最长连续子字符串的长度是 3 。

注意，如果字符串中不存在 0 ，此时认为由 0 组成的最长连续子字符串的长度是 0 。字符串中不存在 1 的情况也适用此规则。


```
impl Solution {
    pub fn check_zero_ones(s: String) -> bool {
        // 一次遍历：分别记录当前连续段长度和各自的历史最大值
        let (mut max1, mut max0) = (0, 0);
        let (mut cur1, mut cur0) = (0, 0);

        for b in s.bytes() {
            if b == b'1' {
                cur1 += 1;
                cur0 = 0;
                max1 = max1.max(cur1);
            } else {
                cur0 += 1;
                cur1 = 0;
                max0 = max0.max(cur0);
            }
        }

        max1 > max0
    }
}
```
