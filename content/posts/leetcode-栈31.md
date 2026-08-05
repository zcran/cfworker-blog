---
title: "leetcode-栈31"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 删除字符串中的所有相邻重复项 II

给你一个字符串 s，「k 倍重复项删除操作」将会从 s 中选择 k 个相邻且相等的字母，并删除它们，使被删去的字符串的左侧和右侧连在一起。

你需要对 s 重复进行无限次这样的删除操作，直到无法继续为止。

在执行完所有删除操作后，返回最终得到的字符串。

本题答案保证唯一。


```
impl Solution {
    pub fn remove_duplicates(s: String, k: i32) -> String {
        let k = k as usize;
        let mut stack: Vec<(char, usize)> = Vec::with_capacity(s.len());

        for ch in s.chars() {
            if let Some((last_char, count)) = stack.last_mut() {
                if *last_char == ch {
                    *count += 1;
                    if *count == k {
                        stack.pop();  // 连续 k 个相同字符，整组移除
                    }
                    continue;
                }
            }
            stack.push((ch, 1));
        }

        // 展开栈中剩余的字符
        let mut result = String::with_capacity(s.len());
        for (ch, count) in stack {
            result.extend(std::iter::repeat(ch).take(count));
        }
        result
    }
}
```
