---
title: "leetcode-数学23"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 和为零的 N 个不同整数

给你一个整数 n，请你返回 任意 一个由 n 个 各不相同 的整数组成的数组，并且这 n 个数相加和为 0 。


```
impl Solution {
    /// 返回由 n 个互不相同的整数组成的数组，且其和为 0。
    ///
    /// 构造思路：成对放入 (i, -i)，它们的和为零；
    /// 若 n 为奇数，再补一个 0，仍保证各元素互不相同且总和为 0。
    pub fn sum_zero(n: i32) -> Vec<i32> {
        let mut ans = Vec::with_capacity(n as usize);

        // 成对添加正负对称的数
        for i in 1..=n / 2 {
            ans.push(i);
            ans.push(-i);
        }

        // n 为奇数时补充一个 0
        if n & 1 == 1 {
            ans.push(0);
        }

        ans
    }
}
```
