---
title: "leetcode-双指针46"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 情感丰富的文字

有时候人们会用重复写一些字母来表示额外的感受，比如 "hello" -> "heeellooo", "hi" -> "hiii"。我们将相邻字母都相同的一串字符定义为相同字母组，例如："h", "eee", "ll", "ooo"。

对于一个给定的字符串 S ，如果另一个单词能够通过将一些字母组扩张从而使其和 S 相同，我们将这个单词定义为可扩张的（stretchy）。扩张操作定义如下：选择一个字母组（包含字母 c ），然后往其中添加相同的字母 c 使其长度达到 3 或以上。

例如，以 "hello" 为例，我们可以对字母组 "o" 扩张得到 "hellooo"，但是无法以同样的方法得到 "helloo" 因为字母组 "oo" 长度小于 3。此外，我们可以进行另一种扩张 "ll" -> "lllll" 以获得 "helllllooo"。如果 s = "helllllooo"，那么查询词 "hello" 是可扩张的，因为可以对它执行这两种扩张操作使得 query = "hello" -> "hellooo" -> "helllllooo" = s。

输入一组查询单词，输出其中可扩张的单词数量。


```
impl Solution {
    pub fn expressive_words(s: String, words: Vec<String>) -> i32 {
        let s_bytes = s.as_bytes();
        let mut count = 0;

        'word_loop: for word in words {
            let w_bytes = word.as_bytes();
            let (mut i, mut j) = (0, 0);
            let (m, n) = (s_bytes.len(), w_bytes.len());

            while i < m && j < n {
                if s_bytes[i] != w_bytes[j] {
                    continue 'word_loop;
                }

                // 统计 s 中当前字符连续长度
                let mut s_len = 1;
                while i + s_len < m && s_bytes[i + s_len] == s_bytes[i] {
                    s_len += 1;
                }

                // 统计 word 中当前字符连续长度
                let mut w_len = 1;
                while j + w_len < n && w_bytes[j + w_len] == w_bytes[j] {
                    w_len += 1;
                }

                // 扩张规则：
                // 1. word 中字符组长度不能超过 s 中的长度
                // 2. 如果长度不同，s 中的长度必须至少为 3（才能扩张）
                if w_len > s_len || (s_len != w_len && s_len < 3) {
                    continue 'word_loop;
                }

                i += s_len;
                j += w_len;
            }

            // 两个字符串必须同时遍历完
            if i == m && j == n {
                count += 1;
            }
        }

        count
    }
}
```
