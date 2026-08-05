---
title: "leetcode-栈25"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 检查替换后的词是否有效

给你一个字符串 s ，请你判断它是否 有效 。
字符串 s 有效 需要满足：假设开始有一个空字符串 t = "" ，你可以执行 任意次 下述操作将 t 转换为 s ：

将字符串 "abc" 插入到 t 中的任意位置。形式上，t 变为 tleft + "abc" + tright，其中 t == tleft + tright 。注意，tleft 和 tright 可能为 空 。

如果字符串 s 有效，则返回 true；否则，返回 false。


```
impl Solution {
    pub fn is_valid(s: String) -> bool {
        // 使用 Vec 作为栈，存储尚未匹配的字符
        let mut stack = Vec::with_capacity(s.len());

        for ch in s.bytes() {
            if ch == b'c' {
                // 尝试弹出 'b' 和 'a'，若失败则无效
                if stack.len() < 2 || stack.pop() != Some(b'b') || stack.pop() != Some(b'a') {
                    return false;
                }
            } else {
                // 只允许 'a' 和 'b' 入栈
                stack.push(ch);
            }
        }

        // 所有字符必须被匹配完
        stack.is_empty()
    }
}
```
