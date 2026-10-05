---
title: "leetcode-贪心30"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 不含 AAA 或 BBB 的字符串

给定两个整数 a 和 b ，返回 任意 字符串 s ，要求满足：

s 的长度为 a + b，且正好包含 a 个 'a' 字母与 b 个 'b' 字母；
子串 'aaa' 没有出现在 s 中；
子串 'bbb' 没有出现在 s 中。


```
impl Solution {
    pub fn str_without3a3b(mut a: i32, mut b: i32) -> String {
        // 贪心：每次输出较多数量的字符（最多连续 2 个），再补 1 个较少字符
        // 若两者相等，则交替输出一个 'a' 和一个 'b'
        let mut ans = String::with_capacity((a + b) as usize);

        while a > 0 || b > 0 {
            if a > b {
                for _ in 0..2.min(a) {
                    ans.push('a');
                    a -= 1;
                }
                if b > 0 {
                    ans.push('b');
                    b -= 1;
                }
            } else if b > a {
                for _ in 0..2.min(b) {
                    ans.push('b');
                    b -= 1;
                }
                if a > 0 {
                    ans.push('a');
                    a -= 1;
                }
            } else {
                ans.push('a');
                a -= 1;
                if b > 0 {
                    ans.push('b');
                    b -= 1;
                }
            }
        }

        ans
    }
}
```
