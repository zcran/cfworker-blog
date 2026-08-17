---
title: "leetcode-模拟58"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 向字符串添加空格

给你一个下标从 0 开始的字符串 s ，以及一个下标从 0 开始的整数数组 spaces 。

数组 spaces 描述原字符串中需要添加空格的下标。每个空格都应该插入到给定索引处的字符值 之前 。

例如，s = "EnjoyYourCoffee" 且 spaces = [5, 9] ，那么我们需要在 'Y' 和 'C' 之前添加空格，这两个字符分别位于下标 5 和下标 9 。因此，最终得到 "Enjoy Your Coffee" 。

请你添加空格，并返回修改后的字符串。


```
impl Solution {
    pub fn add_spaces(s: String, spaces: Vec<i32>) -> String {
        let mut res = String::with_capacity(s.len() + spaces.len());
        let bytes = s.as_bytes();
        let mut sp = 0;  // spaces 数组的当前下标

        for (i, &ch) in bytes.iter().enumerate() {
            // 当前位置需要插入空格（spaces 是有序的，只需检查当前指针）
            if sp < spaces.len() && spaces[sp] as usize == i {
                res.push(' ');
                sp += 1;
            }
            res.push(ch as char);
        }

        res
    }
}
```
