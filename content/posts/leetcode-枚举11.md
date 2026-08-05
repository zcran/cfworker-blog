---
title: "leetcode-枚举11"
date: 2026-07-09T10:04:59+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 执行操作后字典序最小的字符串

给你一个字符串 s 以及两个整数 a 和 b 。其中，字符串 s 的长度为偶数，且仅由数字 0 到 9 组成。

你可以在 s 上按任意顺序多次执行下面两个操作之一：

累加：将  a 加到 s 中所有下标为奇数的元素上（下标从 0 开始）。当数字超过 9 时，从 0 重新循环计算。例如，s = "3456" 且 a = 5，则执行此操作后 s 变成 "3951"。

轮转：将 s 向右轮转 b 位。例如，s = "3456" 且 b = 1，则执行此操作后 s 变成 "6345"。

请你返回在 s 上执行上述操作任意次后可以得到的 字典序最小 的字符串。

如果两个字符串长度相同，那么字符串 a 字典序比字符串 b 小可以这样定义：在 a 和 b 出现不同的第一个位置上，字符串 a 中的字符出现在字母表中的时间早于 b 中的对应字符。例如，"0158” 字典序比 "0190" 小，因为不同的第一个位置是在第三个字符，显然 '5' 出现在 '9' 之前。


```
impl Solution {
    /// 返回经过任意次累加（奇数位加a）和轮转（右移b位）后，字典序最小的字符串。
    pub fn find_lex_smallest_string(s: String, a: i32, b: i32) -> String {
        let n = s.len();
        let b = b as usize % n; // 轮转实际有效步数
        let chars: Vec<char> = s.chars().collect();
        let mut best = s;

        // 枚举所有可能的轮转起始位置（最多 n/gcd(n,b) 种）
        let mut start = 0;
        let mut visited = vec![false; n];

        while !visited[start] {
            visited[start] = true;

            // 对当前轮转状态，枚举奇数位和偶数位的累加次数
            // 奇数位加 a 的次数 j（0..9），偶数位加 a 的次数 k（0..9）
            // 但若 b 为偶数，偶数位不会因轮转而改变，所以只需考虑一种累加模式
            let even_limit = if b % 2 == 0 { 0 } else { 9 };

            for odd_add in 0..10 {
                for even_add in 0..=even_limit {
                    let mut curr: Vec<char> = Vec::with_capacity(n);

                    for idx in 0..n {
                        let orig_idx = (start + idx) % n;
                        let mut digit = chars[orig_idx].to_digit(10).unwrap() as i32;

                        // 根据原始下标奇偶性累加
                        if orig_idx % 2 == 1 {
                            digit = (digit + odd_add * a) % 10;
                        } else {
                            digit = (digit + even_add * a) % 10;
                        }

                        curr.push(std::char::from_digit(digit as u32, 10).unwrap());
                    }

                    let candidate: String = curr.into_iter().collect();
                    if candidate < best {
                        best = candidate;
                    }
                }
            }

            start = (start + b) % n;
        }

        best
    }
}
```
