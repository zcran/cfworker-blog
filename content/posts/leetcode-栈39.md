---
title: "leetcode-栈39"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 删除子字符串的最大得分

给你一个字符串 s 和两个整数 x 和 y 。你可以执行下面两种操作任意次。

· 删除子字符串 "ab" 并得到 x 分。
    · 比方说，从 "cabxbae" 删除 ab ，得到 "cxbae" 。

· 删除子字符串"ba" 并得到 y 分。
    · 比方说，从 "cabxbae" 删除 ba ，得到 "cabxe" 。

请返回对 s 字符串执行上面操作若干次能得到的最大得分。


```
impl Solution {
    pub fn maximum_gain(s: String, x: i32, y: i32) -> i32 {
        // 贪心策略：总是优先删除得分更高的子串
        // 为了简化处理，确保 x >= y，优先删除 "ab"
        let (first, second, first_score, second_score) = if x >= y {
            ('a', 'b', x, y)
        } else {
            ('b', 'a', y, x)
        };

        let mut total_score = 0;
        let mut stack = Vec::with_capacity(s.len());

        // 第一遍：优先删除高分对 first+second
        for ch in s.chars() {
            if !stack.is_empty() && stack.last() == Some(&first) && ch == second {
                stack.pop();
                total_score += first_score;
            } else {
                stack.push(ch);
            }
        }

        // 第二遍：在剩余字符串中删除低分对 second+first
        let mut remaining = Vec::with_capacity(stack.len());
        for ch in stack {
            if !remaining.is_empty() && remaining.last() == Some(&second) && ch == first {
                remaining.pop();
                total_score += second_score;
            } else {
                remaining.push(ch);
            }
        }

        total_score
    }
}
```
