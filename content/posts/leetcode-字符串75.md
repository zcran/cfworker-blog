---
title: "leetcode-字符串75"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 找到初始输入字符串 I

Alice 正在她的电脑上输入一个字符串。但是她打字技术比较笨拙，她 可能 在一个按键上按太久，导致一个字符被输入 多次 。

尽管 Alice 尽可能集中注意力，她仍然可能会犯错 至多 一次。

给你一个字符串 word ，它表示 最终 显示在 Alice 显示屏上的结果。

请你返回 Alice 一开始可能想要输入字符串的总方案数。


```
impl Solution {
    pub fn possible_string_count(word: String) -> i32 {
        // 每有一对相邻相同字符，就对应一种少按一次的可能
        let b = word.as_bytes();
        let mut ans = 1;
        for i in 1..b.len() {
            if b[i] == b[i - 1] {
                ans += 1;
            }
        }
        ans
    }
}
```
