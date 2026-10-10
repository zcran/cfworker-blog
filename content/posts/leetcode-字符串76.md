---
title: "leetcode-字符串76"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 检查平衡字符串

给你一个仅由数字 0 - 9 组成的字符串 num。如果偶数下标处的数字之和等于奇数下标处的数字之和，则认为该数字字符串是一个 平衡字符串。

如果 num 是一个 平衡字符串，则返回 true；否则，返回 false。


```
impl Solution {
    pub fn is_balanced(num: String) -> bool {
        // 偶数位加、奇数位减，最终为 0 即平衡
        num.bytes()
            .enumerate()
            .map(|(i, b)| {
                let d = (b - b'0') as i32;
                if i % 2 == 0 { d } else { -d }
            })
            .sum::<i32>()
            == 0
    }
}
```
