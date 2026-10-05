---
title: "leetcode-双指针56"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 长按键入

你的朋友正在使用键盘输入他的名字 name。偶尔，在键入字符 c 时，按键可能会被长按，而字符可能被输入 1 次或多次。

你将会检查键盘输入的字符 typed。如果它对应的可能是你的朋友的名字（其中一些字符可能被长按），那么就返回 True。


```
impl Solution {
    pub fn is_long_pressed_name(name: String, typed: String) -> bool {
        let name_bytes = name.as_bytes();
        let typed_bytes = typed.as_bytes();
        let (m, n) = (name_bytes.len(), typed_bytes.len());

        let mut i = 0; // name 指针
        let mut j = 0; // typed 指针

        while i < m && j < n {
            if name_bytes[i] != typed_bytes[j] {
                return false;
            }

            // 统计 name 中当前字符的连续出现次数
            let mut count_name = 1;
            while i + count_name < m && name_bytes[i + count_name] == name_bytes[i] {
                count_name += 1;
            }

            // 统计 typed 中当前字符的连续出现次数
            let mut count_typed = 1;
            while j + count_typed < n && typed_bytes[j + count_typed] == typed_bytes[j] {
                count_typed += 1;
            }

            // typed 中该字符的出现次数必须不少于 name 中的次数
            if count_typed < count_name {
                return false;
            }

            i += count_name;
            j += count_typed;
        }

        // 两个字符串必须同时遍历完
        i == m && j == n
    }
}
```
