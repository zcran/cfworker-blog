---
title: "leetcode-单调栈33"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 含特定字母的最小子序列


给你一个字符串 s ，一个整数 k ，一个字母 letter 以及另一个整数 repetition 。

返回 s 中长度为 k 且 字典序最小 的子序列，该子序列同时应满足字母 letter 出现 至少 repetition 次。生成的测试用例满足 letter 在 s 中出现 至少 repetition 次。

子序列 是由原字符串删除一些（或不删除）字符且不改变剩余字符顺序得到的剩余字符串。

字符串 a 字典序比字符串 b 小的定义为：在 a 和 b 出现不同字符的第一个位置上，字符串 a 的字符在字母表中的顺序早于字符串 b 的字符。

```
impl Solution {
    pub fn smallest_subsequence(s: String, k: i32, letter: char, rep: i32) -> String {
        let n = s.len();
        let k = k as usize;
        let rep = rep as usize;
        let mut cc = s.chars().filter(|&c| c == letter).count(); // 剩余 letter 个数
        let mut cnt = 0;          // 已选中的 letter 个数
        let mut ans = vec![];

        for i in 0..n {
            let c = s.as_bytes()[i] as char;

            // 弹出条件：栈顶 > c 且弹出后 letter 数量够 rep，且总长度够 k
            while !ans.is_empty()
                && ans.last() > Some(&c)
                && cnt - if ans.last() == Some(&letter) { 1 } else { 0 } + cc >= rep
                && n - i + ans.len() - 1 >= k
            {
                cnt -= if ans.pop() == Some(letter) { 1 } else { 0 };
            }

            // 入栈条件：长度 < k 且入栈后 letter 数量够 rep
            if ans.len() < k && k - ans.len() - 1 + cnt + if c == letter { 1 } else { 0 } >= rep {
                ans.push(c);
                if c == letter {
                    cnt += 1;
                }
            }

            if c == letter {
                cc -= 1;
            }
        }

        ans.iter().collect()
    }
}
```
