---
title: "leetcode-贪心125"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## k-avoiding 数组的最小总和

给你两个整数 n 和 k 。

对于一个由 不同 正整数组成的数组，如果其中不存在任何求和等于 k 的不同元素对，则称其为 k-avoiding 数组。

返回长度为 n 的 k-avoiding 数组的可能的最小总和。


```
impl Solution {
    pub fn minimum_sum(n: i32, k: i32) -> i32 {
        // 贪心：为使总和最小，取最小的正整数，但不能同时包含和为 k 的两个数。
        // 因此先取 1..k/2（这些数之间不会有配对和为 k），
        // 若还不够 n 个，再从 k 开始往后取（避开 k - x 落在已选集合中的情况）。
        let half = k / 2;
        if n <= half {
            // 只需 1 + 2 + ... + n
            (1 + n) * n / 2
        } else {
            // 1..half 之和 + 从 k 开始的 (n - half) 个连续整数之和
            let cnt = n - half;
            (1 + half) * half / 2 + (k + k + cnt - 1) * cnt / 2
        }
    }
}
```
