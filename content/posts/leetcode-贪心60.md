---
title: "leetcode-贪心60"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 检查字符串是否可以通过排序子字符串得到另一个字符串

给你两个字符串 s 和 t ，请你通过若干次以下操作将字符串 s 转化成字符串 t ：

选择 s 中一个 非空 子字符串并将它包含的字符就地 升序 排序。

比方说，对下划线所示的子字符串进行操作可以由 "14234" 得到 "12344" 。

如果可以将字符串 s 变成 t ，返回 true 。否则，返回 false 。

一个 子字符串 定义为一个字符串中连续的若干字符。


```
impl Solution {
    pub fn is_transformable(s: String, t: String) -> bool {
        let s = s.as_bytes();
        let t = t.as_bytes();

        // pos[d] 记录数字 d 在 s 中出现的所有下标（按顺序）
        let mut pos: [std::collections::VecDeque<usize>; 10] = Default::default();
        for (i, &b) in s.iter().enumerate() {
            pos[(b - b'0') as usize].push_back(i);
        }

        for &b in t {
            let d = (b - b'0') as usize;
            let front = match pos[d].front() {
                Some(&i) => i,
                None => return false, // t 中该数字比 s 中多
            };
            // 若存在比 d 小且位置更靠前的数字，它们会挡住 d，无法通过升序排序让 d 提前
            for small in 0..d {
                if let Some(&i) = pos[small].front() {
                    if i < front {
                        return false;
                    }
                }
            }
            pos[d].pop_front();
        }
        true
    }
}
```
