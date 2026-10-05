---
title: "leetcode-贪心75"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


##  构成交替字符串需要的最小交换次数

给你一个二进制字符串 s ，现需要将其转化为一个 交替字符串 。请你计算并返回转化所需的 最小 字符交换次数，如果无法完成转化，返回 -1 。

交替字符串 是指：相邻字符之间不存在相等情况的字符串。例如，字符串 "010" 和 "1010" 属于交替字符串，但 "0100" 不是。

任意两个字符都可以进行交换，不必相邻 。




```
impl Solution {
    pub fn min_swaps(s: String) -> i32 {
        let n = s.len();
        let zeros = s.bytes().filter(|&b| b == b'0').count();
        let ones = n - zeros;

        // 交替串中 0 和 1 的数量差不能超过 1
        if zeros.abs_diff(ones) > 1 {
            return -1;
        }

        // 统计 s 与「以 0 开头」交替串的差异位数
        let diff = s
            .bytes()
            .enumerate()
            .filter(|&(i, b)| (b == b'0') != (i % 2 == 0))
            .count();

        // 两种目标串的差异：以 0 开头为 diff，以 1 开头为 n - diff
        // 取可行的那种，交换次数为差异数的一半
        if n % 2 == 0 {
            (diff.min(n - diff) / 2) as i32
        } else if zeros > ones {
            // 0 多一个，必须以 0 开头
            (diff / 2) as i32
        } else {
            // 1 多一个，必须以 1 开头
            ((n - diff) / 2) as i32
        }
    }
}
```
