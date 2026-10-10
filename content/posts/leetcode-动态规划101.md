---
title: "leetcode-动态规划101"
date: 2026-10-05T20:40:00+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 字符串的总引力

字符串的 引力 定义为：字符串中 不同 字符的数量。

例如，"abbca" 的引力为 3 ，因为其中有 3 个不同字符 'a'、'b' 和 'c' 。

给你一个字符串 s ，返回 其所有子字符串的总引力 。

子字符串 定义为：字符串中的一个连续字符序列。


```
impl Solution {
    pub fn appeal_sum(s: String) -> i64 {
        // last[c]: 字符 c 上一次出现的下标，-1 表示尚未出现
        let mut last = [-1i32; 26];
        let mut ans = 0i64;
        // sum_g: 以当前字符结尾的所有子串的引力之和
        let mut sum_g = 0i64;

        for (i, b) in s.bytes().enumerate() {
            let i = i as i32;
            let c = (b - b'a') as usize;
            // 当前字符对以 i 结尾的子串贡献：i - last[c]
            sum_g += (i - last[c]) as i64;
            ans += sum_g;
            last[c] = i;
        }

        ans
    }
}
```
