---
title: "leetcode-双指针31"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最长特殊序列 II

给定字符串列表 strs ，返回其中 最长的特殊序列 的长度。如果最长特殊序列不存在，返回 -1 。

特殊序列 定义如下：该序列为某字符串 独有的子序列（即不能是其他字符串的子序列）。

 s 的 子序列可以通过删去字符串 s 中的某些字符实现。

例如，"abc" 是 "aebdc" 的子序列，因为您可以删除"aebdc"中的下划线字符来得到 "abc" 。"aebdc"的子序列还包括"aebdc"、 "aeb" 和 "" (空字符串)。


```
impl Solution {
    /// 判断 s 是否为 t 的子序列
    fn is_subsequence(s: &[u8], t: &[u8]) -> bool {
        let mut si = 0;
        for &c in t {
            if c == s[si] {
                si += 1;
                if si == s.len() {
                    return true;
                }
            }
        }
        false
    }

    pub fn find_lu_slength(strs: Vec<String>) -> i32 {
        let strs_bytes: Vec<&[u8]> = strs.iter().map(|s| s.as_bytes()).collect();
        let mut max_len = -1;

        for i in 0..strs_bytes.len() {
            let s = strs_bytes[i];
            // 剪枝：当前字符串不可能超过已找到的最大长度
            if s.len() as i32 <= max_len {
                continue;
            }

            // 检查 s 是否是其他某个字符串的子序列
            let mut is_unique = true;
            for j in 0..strs_bytes.len() {
                if i != j && Self::is_subsequence(s, strs_bytes[j]) {
                    is_unique = false;
                    break;
                }
            }

            if is_unique {
                max_len = s.len() as i32;
            }
        }

        max_len
    }
}
```
