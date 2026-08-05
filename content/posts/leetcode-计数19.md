---
title: "leetcode-计数19"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 分割平衡字符串

平衡字符串 中，'L' 和 'R' 字符的数量是相同的。

给你一个平衡字符串 s，请你将它分割成尽可能多的子字符串，并满足：

每个子字符串都是平衡字符串。

返回可以通过分割得到的平衡字符串的 最大数量 。


```
impl Solution {
    pub fn balanced_string_split(s: String) -> i32 {
        let mut balance = 0; // 当前平衡值：R 加1，L 减1
        let mut count = 0;   // 平衡子串的数量

        for ch in s.chars() {
            if ch == 'R' {
                balance += 1;
            } else if ch == 'L' {
                balance -= 1;
            }

            // 当平衡值为0时，说明找到了一个平衡子串
            if balance == 0 {
                count += 1;
            }
        }

        count
    }
}
```
