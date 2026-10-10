---
title: "leetcode-动态规划112"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 找到最大开销的子字符串

给你一个字符串 s ，一个字符 互不相同 的字符串 chars 和一个长度与 chars 相同的整数数组 vals 。

子字符串的开销 是一个子字符串中所有字符对应价值之和。空字符串的开销是 0 。

字符的价值 定义如下：

· 如果字符不在字符串 chars 中，那么它的价值是它在字母表中的位置（下标从 1 开始）。
    · 比方说，'a' 的价值为 1 ，'b' 的价值为 2 ，以此类推，'z' 的价值为 26 。
· 否则，如果这个字符在 chars 中的位置为 i ，那么它的价值就是 vals[i] 。

请你返回字符串 s 的所有子字符串中的最大开销。


```
impl Solution {
    pub fn maximum_cost_substring(s: String, chars: String, vals: Vec<i32>) -> i32 {
        // 先默认所有字符的价值为其在字母表中的位置
        let mut value = [0i32; 26];
        for i in 0..26 {
            value[i] = i as i32 + 1;
        }
        // 再用 chars 和 vals 覆盖对应字符的价值（注意 vals 可能为 0）
        for (i, b) in chars.bytes().enumerate() {
            value[(b - b'a') as usize] = vals[i];
        }

        // Kadane 算法求最大子数组和（允许空子串，下限为 0）
        let mut ans = 0;
        let mut cur = 0;
        for b in s.bytes() {
            cur = cur.max(0) + value[(b - b'a') as usize];
            ans = ans.max(cur);
        }

        ans
    }
}
```
