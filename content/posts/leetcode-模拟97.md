---
title: "leetcode-模拟97"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 故障键盘

你的笔记本键盘存在故障，每当你在上面输入字符 'i' 时，它会反转你所写的字符串。而输入其他字符则可以正常工作。

给你一个下标从 0 开始的字符串 s ，请你用故障键盘依次输入每个字符。

返回最终笔记本屏幕上输出的字符串。


```
use std::collections::VecDeque;

impl Solution {
    pub fn final_string(s: String) -> String {
        let mut dq = VecDeque::new();
        let mut rev = false; // true 表示当前字符串处于反转状态

        for ch in s.chars() {
            if ch == 'i' {
                // 遇到 'i'，切换方向标记，无需真正反转
                rev = !rev;
            } else if rev {
                // 反转状态下，新字符加到头部（等效于反转后追加到尾部）
                dq.push_front(ch);
            } else {
                // 正常状态下，追加到尾部
                dq.push_back(ch);
            }
        }

        // 若最终为反转状态，收集时倒序输出
        if rev {
            dq.into_iter().rev().collect()
        } else {
            dq.into_iter().collect()
        }
    }
}
```
