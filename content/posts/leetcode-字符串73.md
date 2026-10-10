---
title: "leetcode-字符串73"
date: 2026-10-10T12:56:57+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 压缩字符串 III

给你一个字符串 word，请你使用以下算法进行压缩：

· 从空字符串 comp 开始。当 word 不为空 时，执行以下操作：
    · 移除 word 的最长单字符前缀，该前缀由单一字符 c 重复多次组成，且该前缀长度 最多 为 9 。
    · 将前缀的长度和字符 c 追加到 comp 。

返回字符串 comp 。




```
impl Solution {
    pub fn compressed_string(word: String) -> String {
        let b = word.as_bytes();
        let mut result = String::with_capacity(b.len() * 2);
        let mut i = 0;

        while i < b.len() {
            let c = b[i];
            // 统计当前字符连续出现次数，最多 9 个一组
            let mut count = 0;
            while i < b.len() && b[i] == c && count < 9 {
                count += 1;
                i += 1;
            }
            result.push((b'0' + count) as char);
            result.push(c as char);
        }

        result
    }
}
```
