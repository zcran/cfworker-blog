---
title: "leetcode-贪心121"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 使所有字符相等的最小成本

给你一个下标从 0 开始、长度为 n 的二进制字符串 s ，你可以对其执行两种操作：

选中一个下标 i 并且反转从下标 0 到下标 i（包括下标 0 和下标 i ）的所有字符，成本为 i + 1 。

选中一个下标 i 并且反转从下标 i 到下标 n - 1（包括下标 i 和下标 n - 1 ）的所有字符，成本为 n - i 。

返回使字符串内所有字符 相等 需要的 最小成本 。

反转 字符意味着：如果原来的值是 '0' ，则反转后值变为 '1' ，反之亦然。


```
impl Solution {
    pub fn minimum_cost(s: String) -> i64 {
        // 观察：反转前缀 / 后缀相当于在相邻不同字符处"翻转"状态。
        // 把所有字符变相同，只需消除所有相邻的 01 / 10 分界，
        // 在每个分界处花 min(前缀成本, 后缀成本) 即可。
        // 分界位置 i（s[i] != s[i-1]）的前缀成本为 i，后缀成本为 n - i。
        let n = s.len();
        let bytes = s.as_bytes();
        let mut ans = 0i64;

        for i in 1..n {
            if bytes[i] != bytes[i - 1] {
                ans += (i as i64).min(n as i64 - i as i64);
            }
        }
        ans
    }
}
```
