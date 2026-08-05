---
title: "leetcode-栈28"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 删除字符串中的所有相邻重复项

给出由小写字母组成的字符串 s，重复项删除操作会选择两个相邻且相同的字母，并删除它们。

在 s 上反复执行重复项删除操作，直到无法继续删除。

在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。


```
impl Solution {
    pub fn remove_duplicates(s: String) -> String {
        let mut stack = Vec::with_capacity(s.len());

        for ch in s.chars() {
            if stack.last() == Some(&ch) {
                stack.pop();  // 当前字符与栈顶相同，消除
            } else {
                stack.push(ch);  // 不同则入栈
            }
        }

        stack.into_iter().collect()
    }
}
```
