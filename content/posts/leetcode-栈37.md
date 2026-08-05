---
title: "leetcode-栈37"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 括号的最大嵌套深度

给定 有效括号字符串 s，返回 s 的 嵌套深度。嵌套深度是嵌套括号的 最大 数量。


```
impl Solution {
    pub fn max_depth(s: String) -> i32 {
        let mut max_depth = 0;
        let mut current_depth = 0;

        for ch in s.chars() {
            match ch {
                '(' => {
                    current_depth += 1;
                    max_depth = max_depth.max(current_depth);
                }
                ')' => {
                    current_depth -= 1;
                }
                _ => {}  // 忽略非括号字符
            }
        }

        max_depth
    }
}
```
