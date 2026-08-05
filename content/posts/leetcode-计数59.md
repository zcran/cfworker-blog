---
title: "leetcode-计数59"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 制造字母异位词的最小步骤数 II

给你两个字符串 s 和 t 。在一步操作中，你可以给 s 或者 t 追加 任一字符 。

返回使 s 和 t 互为 字母异位词 所需的最少步骤数。

字母异位词 指字母相同但是顺序不同（或者相同）的字符串。


```
impl Solution {
    pub fn min_steps(s: String, t: String) -> i32 {
        // 用一个数组记录每个字母的净差值（s - t）
        let mut diff = [0i32; 26];

        // s 中的字母增加计数
        for b in s.bytes() {
            diff[(b - b'a') as usize] += 1;
        }

        // t 中的字母减少计数
        for b in t.bytes() {
            diff[(b - b'a') as usize] -= 1;
        }

        // 所有字母差值的绝对值之和，即为需要追加的字符数
        diff.iter().map(|&x| x.abs()).sum()
    }
}
```
