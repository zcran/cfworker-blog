---
title: "leetcode-双指针32"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 通过删除字母匹配到字典里最长单词

给你一个字符串 s 和一个字符串数组 dictionary ，找出并返回 dictionary 中最长的字符串，该字符串可以通过删除 s 中的某些字符得到。

如果答案不止一个，返回长度最长且字母序最小的字符串。如果答案不存在，则返回空字符串。


```
impl Solution {
    /// 判断 word 是否为 s 的子序列
    fn is_subsequence(word: &[u8], s: &[u8]) -> bool {
        let mut si = 0;
        for &c in word {
            // 在 s 中查找当前字符
            while si < s.len() && s[si] != c {
                si += 1;
            }
            if si == s.len() {
                return false;
            }
            si += 1; // 匹配成功，移动到下一个位置
        }
        true
    }

    pub fn find_longest_word(s: String, mut dictionary: Vec<String>) -> String {
        // 排序：长度降序，长度相同则字典序升序
        dictionary.sort_by(|a, b| {
            b.len().cmp(&a.len()).then_with(|| a.cmp(b))
        });

        let s_bytes = s.as_bytes();

        // 按排序顺序检查，第一个匹配的就是答案
        for word in dictionary {
            if Self::is_subsequence(word.as_bytes(), s_bytes) {
                return word;
            }
        }

        String::new()
    }
}
```
