---
title: "leetcode-哈希表11"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 键盘行

给你一个字符串数组 words ，只返回可以使用在 美式键盘 同一行的字母打印出来的单词。

请注意，字符串 不区分大小写，相同字母的大小写形式都被视为在同一行。

美式键盘 中：

第一行由字符 "qwertyuiop" 组成。
第二行由字符 "asdfghjkl" 组成。
第三行由字符 "zxcvbnm" 组成。


```
impl Solution {
    pub fn find_words(words: Vec<String>) -> Vec<String> {
        // 用长度为 26 的数组记录每个字母所在行号（0/1/2），查表 O(1) 且无哈希开销。
        // 行号不匹配时立即短路，避免构建集合。
        let row = |c: u8| -> u8 {
            match c {
                b'q' | b'w' | b'e' | b'r' | b't' | b'y' | b'u' | b'i' | b'o' | b'p' => 0,
                b'a' | b's' | b'd' | b'f' | b'g' | b'h' | b'j' | b'k' | b'l' => 1,
                _ => 2,
            }
        };

        words
            .into_iter()
            .filter(|w| {
                let mut bytes = w.bytes().map(|b| b.to_ascii_lowercase());
                let first = row(bytes.next().unwrap());
                // 所有字母都必须与首字母在同一行。
                bytes.all(|b| row(b) == first)
            })
            .collect()
    }
}
```
