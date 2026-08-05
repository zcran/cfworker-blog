---
title: "leetcode-计数18"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## “气球” 的最大数量

给你一个字符串 text，你需要使用 text 中的字母来拼凑尽可能多的单词 "balloon"（气球）。

字符串 text 中的每个字母最多只能被使用一次。请你返回最多可以拼凑出多少个单词 "balloon"。


```
impl Solution {
    pub fn max_number_of_balloons(text: String) -> i32 {
        // 统计组成 "balloon" 所需的5种字母出现次数
        // 索引对应: b, a, l, o, n
        let mut count = [0; 5];

        for ch in text.chars() {
            match ch {
                'b' => count[0] += 1,
                'a' => count[1] += 1,
                'l' => count[2] += 1,
                'o' => count[3] += 1,
                'n' => count[4] += 1,
                _ => {}
            }
        }

        // 'l' 和 'o' 在 "balloon" 中各出现两次
        count[2] /= 2; // l 的数量减半
        count[3] /= 2; // o 的数量减半

        // 能拼出的单词数受限于出现次数最少的字母
        *count.iter().min().unwrap()
    }
}
```
