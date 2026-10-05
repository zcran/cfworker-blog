---
title: "leetcode-贪心57"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最少的后缀翻转次数

给你一个长度为 n 、下标从 0 开始的二进制字符串 target 。你自己有另一个长度为 n 的二进制字符串 s ，最初每一位上都是 0 。你想要让 s 和 target 相等。

在一步操作，你可以选择下标 i（0 <= i < n）并翻转在 闭区间 [i, n - 1] 内的所有位。翻转意味着 '0' 变为 '1' ，而 '1' 变为 '0' 。

返回使 s 与 target 相等需要的最少翻转次数。


```
impl Solution {
    pub fn min_flips(target: String) -> i32 {
        // 只需统计相邻字符不同的次数（含与起始 '0' 的比较）
        let mut flips = 0;
        let mut prev = b'0';
        for b in target.bytes() {
            if b != prev {
                flips += 1;
                prev = b;
            }
        }
        flips
    }
}
```
