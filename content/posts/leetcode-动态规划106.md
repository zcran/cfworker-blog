---
title: "leetcode-动态规划106"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最长理想子序列

给你一个由小写字母组成的字符串 s ，和一个整数 k 。如果满足下述条件，则可以将字符串 t 视作是 理想字符串 ：

t 是字符串 s 的一个子序列。
t 中每两个 相邻 字母在字母表中位次的绝对差值小于或等于 k 。

返回 最长 理想字符串的长度。

字符串的子序列同样是一个字符串，并且子序列还满足：可以经由其他字符串删除某些字符（也可以不删除）但不改变剩余字符的顺序得到。

注意：字母表顺序不会循环。例如，'a' 和 'z' 在字母表中位次的绝对差值是 25 ，而不是 1 。


```
impl Solution {
    pub fn longest_ideal_string(s: String, k: i32) -> i32 {
        // f[c]: 以字符 c 结尾的最长理想字符串长度
        let mut f = [0i32; 26];

        for &b in s.as_bytes() {
            let c = (b - b'a') as usize;
            // 只需在允许范围内查找前驱字符，更新以 c 结尾的最长长度
            let lo = c.saturating_sub(k as usize);
            let hi = (c + k as usize).min(25);
            let best = (lo..=hi).map(|p| f[p]).max().unwrap_or(0);
            f[c] = f[c].max(best + 1);
        }

        *f.iter().max().unwrap()
    }
}
```
