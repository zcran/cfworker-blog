---
title: "leetcode-模拟132"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 反转元音数相同的单词

给你一个字符串 s，它由小写的英文单词组成，每个单词之间用一个空格隔开。

请确定 第一个单词 中的元音字母数。然后，对于每个 后续单词 ，如果它们的元音字母数与第一个单词相同，则将它们 反转 。其余单词保持不变。

返回处理后的结果字符串。

元音字母包括 'a', 'e', 'i', 'o' 和 'u'。


```
impl Solution {
    pub fn reverse_words(s: String) -> String {
        let vowels: [bool; 128] = {
            let mut table = [false; 128];
            for &c in b"aeiou" {
                table[c as usize] = true;
            }
            table
        };

        let mut words = s.split(' ').peekable();
        let first = words.next().unwrap_or("");
        let target = first.bytes().filter(|&c| vowels[c as usize]).count();

        let mut result = String::with_capacity(s.len());
        result.push_str(first);

        for word in words {
            if !result.is_empty() {
                result.push(' ');
            }
            let cnt = word.bytes().filter(|&c| vowels[c as usize]).count();
            if cnt == target {
                result.extend(word.chars().rev());
            } else {
                result.push_str(word);
            }
        }

        result
    }
}
```
