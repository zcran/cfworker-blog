---
title: "leetcode-双指针98"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最少得分子序列

给你两个字符串 s 和 t 。

你可以从字符串 t 中删除任意数目的字符。

如果没有从字符串 t 中删除字符，那么得分为 0 ，否则：

令 left 为删除字符中的最小下标。
令 right 为删除字符中的最大下标。

字符串的得分为 right - left + 1 。

请你返回使 t 成为 s 子序列的最小得分。

一个字符串的 子序列 是从原字符串中删除一些字符后（也可以一个也不删除），剩余字符不改变顺序得到的字符串。（比方说 "ace" 是 "abcde" 的子序列，但是 "aec" 不是）。


```
impl Solution {
    pub fn minimum_score(s: String, t: String) -> i32 {
        let (s, t) = (s.as_bytes(), t.as_bytes());
        let (n, m) = (s.len(), t.len());

        // suf[i] 表示 t[suf[i]..] 是 s[i..] 的子序列（即从 s[i] 开始能匹配 t 的最小后缀起点）
        let mut suf = vec![0usize; n + 1];
        suf[n] = m;

        let mut j = m as isize - 1;
        for i in (0..n).rev() {
            if j >= 0 && s[i] == t[j as usize] {
                j -= 1;
            }
            if j < 0 {
                return 0; // t 本身已是 s 的子序列
            }
            suf[i] = (j + 1) as usize;
        }

        // 初始答案：删除 t[..suf[0]]
        let mut ans = suf[0];

        let mut j = 0usize;
        for i in 0..n {
            if j < m && s[i] == t[j] {
                j += 1;
                // t[..j] 已匹配到 s[..=i]，t[suf[i+1]..] 可匹配 s[i+1..]
                // 需删除 t[j..suf[i+1]]
                ans = ans.min(suf[i + 1] - j);
            }
        }

        ans as i32
    }
}
```
