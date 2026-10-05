---
title: "leetcode-双指针13"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 反转字符串中的单词

给你一个字符串 s ，请你反转字符串中 单词 的顺序。

单词 是由非空格字符组成的字符串。s 中使用至少一个空格将字符串中的 单词 分隔开。

返回 单词 顺序颠倒且 单词 之间用单个空格连接的结果字符串。

注意：输入字符串 s中可能会存在前导空格、尾随空格或者单词间的多个空格。返回的结果字符串中，单词间应当仅用单个空格分隔，且不包含任何额外的空格。


```
impl Solution {
    /// 反转字符串中的单词顺序（不使用额外集合）
    /// 时间复杂度 O(n)，空间复杂度 O(n)
    /// 先反转整个字符串，再反转每个单词，最后处理空格
    pub fn reverse_words(s: String) -> String {
        let mut chars: Vec<char> = s.chars().collect();
        let n = chars.len();

        // 1. 反转整个字符串
        chars.reverse();

        // 2. 反转每个单词
        let mut start = 0;
        while start < n {
            while start < n && chars[start] == ' ' {
                start += 1;
            }
            let mut end = start;
            while end < n && chars[end] != ' ' {
                end += 1;
            }
            chars[start..end].reverse();
            start = end;
        }

        // 3. 清理多余空格并构建结果
        let mut result = String::with_capacity(n);
        let mut i = 0;
        while i < n {
            if chars[i] != ' ' {
                if !result.is_empty() {
                    result.push(' ');
                }
                while i < n && chars[i] != ' ' {
                    result.push(chars[i]);
                    i += 1;
                }
            } else {
                i += 1;
            }
        }

        result
    }
}
```


```
impl Solution {
    pub fn reverse_words(s: String) -> String {
        s.split_ascii_whitespace()
            .rev()
            .collect::<Vec<_>>()
            .join(" ")
    }
}

```
