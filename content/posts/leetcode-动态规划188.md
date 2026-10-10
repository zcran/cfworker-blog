---
title: "leetcode-动态规划188"
date: 2026-10-05T20:40:07+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 回文子串

给定一个字符串 s ，请计算这个字符串中有多少个回文子字符串。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。


```
impl Solution {
    pub fn count_substrings(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        // dp[j]：以 j 结尾的、当前起点的回文状态
        // 使用滚动数组按长度递推：从中心向两边扩展更自然。
        let mut ans = 0i32;

        // 以每个位置为中心（奇回文）或每对相邻位置为中心（偶回文）向外扩展
        for center in 0..n {
            // 奇数长度回文：中心为 center
            let mut l = center as isize;
            let mut r = center as isize;
            while l >= 0 && (r as usize) < n && s[l as usize] == s[r as usize] {
                ans += 1;
                l -= 1;
                r += 1;
            }

            // 偶数长度回文：中心在 center 和 center+1 之间
            let mut l = center as isize;
            let mut r = center as isize + 1;
            while l >= 0 && (r as usize) < n && s[l as usize] == s[r as usize] {
                ans += 1;
                l -= 1;
                r += 1;
            }
        }

        ans
    }
}
```
