---
title: "leetcode-字符串83"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 检查相邻数字差

给你一个由数字组成的字符串 s。

如果每一对 相邻 数字之间的 绝对差 都至多为 2，则返回 true；否则返回 false。

a 和 b 之间的绝对差定义为 abs(a - b)。


```
impl Solution {
    pub fn is_adjacent_diff_at_most_two(s: String) -> bool {
        // 相邻数字字符的 ASCII 差绝对值不超过 2
        s.as_bytes()
            .windows(2)
            .all(|w| (w[0] as i32 - w[1] as i32).abs() <= 2)
    }
}
```
