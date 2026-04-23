---
title: "leetcode-滚动哈希8"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 最长快乐前缀

「快乐前缀」 是在原字符串中既是 非空 前缀也是后缀（不包括原字符串自身）的字符串。

给你一个字符串 s，请你返回它的 最长快乐前缀。如果不存在满足题意的前缀，则返回一个空字符串 "" 。


```
impl Solution {
    /// 返回字符串 `s` 的最长快乐前缀（即最长的真前缀，同时也是后缀）。
    /// 例如 "level" → "l"；"ababab" → "abab"；"a" → ""。
    ///
    /// # 原理
    /// - 使用 KMP 算法的前缀函数（π 函数）：
    ///   π[i] 表示子串 s[0..=i] 的最长真 border 长度（前缀 = 后缀）。
    /// - 整个字符串的最长快乐前缀长度即为 π[n-1]。
    /// - 通过迭代计算 π 数组，时间复杂度 O(n)。
    ///
    /// # 参数
    /// - `s`: 输入字符串
    ///
    /// # 返回值
    /// - 最长快乐前缀（若不存在则返回空字符串）
    pub fn longest_prefix(s: String) -> String {
        let bytes = s.as_bytes();
        let n = bytes.len();
        if n < 2 {
            return String::new();
        }

        // 前缀函数数组：pi[i] 表示以 i 结尾的子串的最长 border 长度
        let mut pi = vec![0; n];

        // 迭代计算前缀函数（函数式风格：使用 fold 状态传递）
        // 维护当前已匹配的长度 j
        let _ = (1..n).fold(0, |mut j, i| {
            // 失配时回退到前一个 border 位置
            while j > 0 && bytes[i] != bytes[j] {
                j = pi[j - 1];
            }
            // 匹配成功则增加长度
            if bytes[i] == bytes[j] {
                j += 1;
            }
            pi[i] = j;
            j // 作为下一个 fold 的初始状态
        });

        // 整个字符串的最长 border 长度
        let border_len = pi[n - 1];
        if border_len == 0 {
            String::new()
        } else {
            s[0..border_len].to_string()
        }
    }
}
```
