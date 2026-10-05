---
title: "leetcode-双指针74"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 构造字典序最大的合并字符串

给你两个字符串 word1 和 word2 。你需要按下述方式构造一个新字符串 merge ：如果 word1 或 word2 非空，选择 下面选项之一 继续操作：

· 如果 word1 非空，将 word1 中的第一个字符附加到 merge 的末尾，并将其从 word1 中移除。
    · 例如，word1 = "abc" 且 merge = "dv" ，在执行此选项操作之后，word1 = "bc" ，同时 merge = "dva" 。

· 如果 word2 非空，将 word2 中的第一个字符附加到 merge 的末尾，并将其从 word2 中移除。
    · 例如，word2 = "abc" 且 merge = "" ，在执行此选项操作之后，word2 = "bc" ，同时 merge = "a" 。

返回你可以构造的字典序 最大 的合并字符串 merge 。

长度相同的两个字符串 a 和 b 比较字典序大小，如果在 a 和 b 出现不同的第一个位置，a 中字符在字母表中的出现顺序位于 b 中相应字符之后，就认为字符串 a 按字典序比字符串 b 更大。例如，"abcd" 按字典序比 "abcc" 更大，因为两个字符串出现不同的第一个位置是第四个字符，而 d 在字母表中的出现顺序位于 c 之后。


```
impl Solution {
    pub fn largest_merge(word1: String, word2: String) -> String {
        let (mut w1, mut w2) = (word1.as_bytes(), word2.as_bytes());
        let mut result = Vec::with_capacity(w1.len() + w2.len());
        let (mut i, mut j) = (0, 0);

        while i < w1.len() && j < w2.len() {
            // 比较两个字符串从当前位置开始的字典序
            if w1[i..] > w2[j..] {
                result.push(w1[i]);
                i += 1;
            } else {
                result.push(w2[j]);
                j += 1;
            }
        }

        // 添加剩余字符
        if i < w1.len() {
            result.extend_from_slice(&w1[i..]);
        }
        if j < w2.len() {
            result.extend_from_slice(&w2[j..]);
        }

        String::from_utf8(result).unwrap()
    }
}
```
