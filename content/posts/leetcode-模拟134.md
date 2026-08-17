---
title: "leetcode-模拟134"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 反转一个字符串里的字母后反转特殊字符

给你一个由小写英文字母和特殊字符组成的字符串 s。

你的任务是 按顺序 执行以下操作：

反转小写字母，并将它们放回原来字母所占据的位置。
反转特殊字符，并将它们放回原来特殊字符所占据的位置。

返回执行反转操作后的结果字符串。


```
impl Solution {
    /// 分别反转小写字母和特殊字符（保持各自原位）
    pub fn reverse_by_type(mut s: String) -> String {
        // 安全：所有字符均为 ASCII 单字节，可直接交换
        let bytes = unsafe { s.as_bytes_mut() };

        // 双指针反转，pred 为 true 表示"跳过该字符"
        let mut reverse = |pred: fn(u8) -> bool| {
            let mut i = 0isize;
            let mut j = bytes.len() as isize - 1;
            while i < j {
                while i < j && pred(bytes[i as usize]) {
                    i += 1;
                }
                while i < j && pred(bytes[j as usize]) {
                    j -= 1;
                }
                bytes.swap(i as usize, j as usize);
                i += 1;
                j -= 1;
            }
        };

        reverse(|b| b.is_ascii_lowercase());   // 跳过小写，交换特殊字符
        reverse(|b| !b.is_ascii_lowercase());  // 跳过特殊字符，交换小写字母

        s
    }
}
```
