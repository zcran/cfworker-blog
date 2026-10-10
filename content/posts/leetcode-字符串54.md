---
title: "leetcode-字符串54"
date: 2026-10-10T12:56:56+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 统计星号

给你一个字符串 s ，每 两个 连续竖线 '|' 为 一对 。换言之，第一个和第二个 '|' 为一对，第三个和第四个 '|' 为一对，以此类推。

请你返回 不在 竖线对之间，s 中 '*' 的数目。

注意，每个竖线 '|' 都会 恰好 属于一个对。


```
impl Solution {
    pub fn count_asterisks(s: String) -> i32 {
        // 用一个布尔标记当前是否位于竖线对内，遇到 '|' 翻转
        let mut inside = false;
        let mut count = 0;

        for b in s.bytes() {
            match b {
                b'|' => inside = !inside,
                b'*' if !inside => count += 1,
                _ => {}
            }
        }

        count
    }
}
```
