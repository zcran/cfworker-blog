---
title: "leetcode-滑动窗口43"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 所有元音按顺序排布的最长子字符串

当一个字符串满足如下条件时，我们称它是 美丽的 ：

所有 5 个英文元音字母（'a' ，'e' ，'i' ，'o' ，'u'）都必须 至少 出现一次。

这些元音字母的顺序都必须按照 字典序 升序排布（也就是说所有的 'a' 都在 'e' 前面，所有的 'e' 都在 'i' 前面，以此类推）

比方说，字符串 "aeiou" 和 "aaaaaaeiiiioou" 都是 美丽的 ，但是 "uaeio" ，"aeoiu" 和 "aaaeeeooo" 不是美丽的 。

给你一个只包含英文元音字母的字符串 word ，请你返回 word 中 最长美丽子字符串的长度 。如果不存在这样的子字符串，请返回 0 。

子字符串 是字符串中一个连续的字符序列。

```
impl Solution {
    pub fn longest_beautiful_substring(word: String) -> i32 {
        let s = word.as_bytes();
        let n = s.len();
        let mut max_len = 0;
        let mut i = 0;

        while i < n {
            // 必须以 'a' 开头
            if s[i] != b'a' {
                i += 1;
                continue;
            }

            let start = i;
            let mut seen = 1; // 已经看到 'a'

            // 扩展窗口，允许相同字符或下一个字典序字符
            i += 1;
            while i < n {
                let curr = s[i];
                let prev = s[i - 1];

                if curr == prev {
                    // 相同字符，继续
                    i += 1;
                } else if (prev == b'a' && curr == b'e')
                    || (prev == b'e' && curr == b'i')
                    || (prev == b'i' && curr == b'o')
                    || (prev == b'o' && curr == b'u')
                {
                    // 按字典序升序
                    seen += 1;
                    i += 1;
                } else {
                    // 不满足条件，终止当前子串
                    break;
                }
            }

            // 如果包含全部 5 个元音，更新答案
            if seen == 5 {
                max_len = max_len.max(i - start);
            }
        }

        max_len as i32
    }
}
```
