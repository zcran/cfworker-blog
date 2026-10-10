---
title: "leetcode-数学83"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 最小全 1 倍数

给你一个正整数 k。

找出满足以下条件的 最小 整数 n：n 能被 k 整除，且其十进制表示中 只包含数字 1（例如：1、11、111、……）。

返回一个整数，表示 n 的十进制表示的 位数 。如果不存在这样的 n，则返回 -1。


```
impl Solution {
    pub fn min_all_one_multiple(k: i32) -> i32 {
        // 若 k 含因子 2 或 5，全 1 数（末位恒为 1）不可能被整除。
        if k % 2 == 0 || k % 5 == 0 {
            return -1;
        }

        // 逐个构造 1, 11, 111, ...，只保留对 k 的余数，避免大数溢出。
        // 由鸽巢原理，最多 k 步必出现余数 0（或重复），故循环上界为 k。
        let mut rem = 0;
        for len in 1..=k {
            rem = (rem * 10 + 1) % k;
            if rem == 0 {
                return len;
            }
        }

        -1
    }
}
```
