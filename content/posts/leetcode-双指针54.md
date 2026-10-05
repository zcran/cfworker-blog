---
title: "leetcode-双指针54"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 仅仅反转字母

给你一个字符串 s ，根据下述规则反转字符串：

所有非英文字母保留在原有位置。
所有英文字母（小写或大写）位置反转。

返回反转后的 s 。


```
impl Solution {
    pub fn reverse_only_letters(s: String) -> String {
        let mut chars: Vec<char> = s.chars().collect();
        let (mut left, mut right) = (0, chars.len() - 1);

        while left < right {
            // 跳过非字母字符
            while left < right && !chars[left].is_ascii_alphabetic() {
                left += 1;
            }
            while left < right && !chars[right].is_ascii_alphabetic() {
                right -= 1;
            }
            // 交换字母
            if left < right {
                chars.swap(left, right);
                left += 1;
                right -= 1;
            }
        }

        chars.into_iter().collect()
    }
}
```
