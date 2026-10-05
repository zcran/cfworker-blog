---
title: "leetcode-二分查找77"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 字符相同的最短子字符串 II

给你一个长度为 n 的二进制字符串 s 和一个整数 numOps。

你可以对 s 执行以下操作，最多 numOps 次：

选择任意下标 i（其中 0 <= i < n），并 翻转 s[i]，即如果 s[i] == '1'，则将 s[i] 改为 '0'，反之亦然。

你需要 最小化 s 的最长 相同 子字符串 的长度，相同子字符串是指子字符串中的所有字符都相同。

返回执行所有操作后可获得的 最小 长度。


```
impl Solution {
    pub fn min_length(s: String, num_ops: i32) -> i32 {
        let s = s.as_bytes();
        let n = s.len();

        // 判断能否把最长相同子串长度压到不超过 mid
        let can = |mid: usize| -> bool {
            // 特殊情况：mid == 1 时，最终串必须形如 "0101..." 或 "1010..."
            if mid == 1 {
                return [b'0', b'1'].iter().any(|&start_bit| {
                    let mut ops = num_ops;
                    let mut expected = start_bit;
                    for &c in s {
                        if c == expected {
                            ops -= 1;
                        }
                        expected ^= 1;
                    }
                    ops >= 0
                });
            }

            // 一般情况：每段长度为 cnt 的相同字符，需要 cnt / (mid + 1) 次翻转
            let mut ops = num_ops;
            let mut cnt = 0;
            let mut last = b' ';
            for &c in s {
                if c == last {
                    cnt += 1;
                } else {
                    ops -= cnt as i32 / (mid as i32 + 1);
                    last = c;
                    cnt = 1;
                }
            }
            ops -= cnt as i32 / (mid as i32 + 1);
            ops >= 0
        };

        // 二分最小可能长度
        let mut left = 1;
        let mut right = n;
        while left < right {
            let mid = left + (right - left) / 2;
            if can(mid) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        left as i32
    }
}
```
