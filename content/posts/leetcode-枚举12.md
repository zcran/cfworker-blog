---
title: "leetcode-枚举12"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 统计只差一个字符的子串数目

给你两个字符串 s 和 t ，请你找出 s 中的非空子串的数目，这些子串满足替换 一个不同字符 以后，是 t 串的子串。换言之，请你找到 s 和 t 串中 恰好 只有一个字符不同的子字符串对的数目。

比方说， "computer" and "computation" 只有一个字符不同： 'e'/'a' ，所以这一对子字符串会给答案加 1 。

请你返回满足上述条件的不同子字符串对数目。

一个 子字符串 是一个字符串中连续的字符。


```
impl Solution {
    /// 统计 s 和 t 中恰好只有一个字符不同的子串对数量。
    pub fn count_substrings(s: String, t: String) -> i32 {
        let s = s.as_bytes();
        let t = t.as_bytes();
        let mut ans = 0;

        /// 统计从 (si, tj) 开始的对角线上的贡献。
        #[inline]
        fn count_diag(s: &[u8], t: &[u8], mut si: usize, mut tj: usize) -> i32 {
            let mut total = 0;
            let mut same = 0;   // 当前连续相同字符数（0个不同）
            let mut diff = 0;   // 当前恰好1个不同的子串数
            while si < s.len() && tj < t.len() {
                if s[si] == t[tj] {
                    same += 1;
                    // diff 保持不变
                } else {
                    diff = same + 1;
                    same = 0;
                }
                total += diff;
                si += 1;
                tj += 1;
            }
            total
        }

        // 枚举所有可能的偏移（s 起始在前，或 t 起始在前）
        for i in 0..s.len() {
            ans += count_diag(s, t, i, 0);
        }
        for j in 1..t.len() {
            ans += count_diag(s, t, 0, j);
        }

        ans
    }
}
```
