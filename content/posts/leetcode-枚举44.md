---
title: "leetcode-枚举44"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 包含三个字符串的最短字符串


给你三个字符串 a ，b 和 c ， 你的任务是找到长度 最短 的字符串，且这三个字符串都是它的 子字符串 。
如果有多个这样的字符串，请你返回 字典序最小 的一个。

请你返回满足题目要求的字符串。

注意：

两个长度相同的字符串 a 和 b ，如果在第一个不相同的字符处，a 的字母在字母表中比 b 的字母 靠前 ，那么字符串 a 比字符串 b 字典序小 。
子字符串 是一个字符串中一段连续的字符序列。

```
impl Solution {
    pub fn minimum_string(a: String, b: String, c: String) -> String {
        let strs = [a, b, c];
        let mut best: Option<String> = None;

        // 尝试所有 6 种排列顺序
        let perms = [
            [0, 1, 2],
            [0, 2, 1],
            [1, 0, 2],
            [1, 2, 0],
            [2, 0, 1],
            [2, 1, 0],
        ];

        for perm in perms {
            // 按顺序合并三个字符串
            let mut merged = merge(&strs[perm[0]], &strs[perm[1]]);
            merged = merge(&merged, &strs[perm[2]]);

            // 选择最短且字典序最小的结果
            match &best {
                None => best = Some(merged),
                Some(b) => {
                    if merged.len() < b.len() || (merged.len() == b.len() && merged < *b) {
                        best = Some(merged);
                    }
                }
            }
        }

        best.unwrap()
    }
}

/// 将 s1 和 s2 合并成一个最短的字符串，使 s1 和 s2 都是其子串
fn merge(s1: &str, s2: &str) -> String {
    let (s1, s2) = (s1.as_bytes(), s2.as_bytes());
    let n1 = s1.len();
    let n2 = s2.len();

    // 检查 s2 是否已经是 s1 的子串
    if n1 >= n2 && contains_substring(s1, s2) {
        return String::from_utf8(s1.to_vec()).unwrap();
    }
    if n2 >= n1 && contains_substring(s2, s1) {
        return String::from_utf8(s2.to_vec()).unwrap();
    }

    // 尝试将 s2 合并到 s1 的末尾，找到最大的重叠部分
    // 从 s1 的后缀与 s2 的前缀的最大重叠开始检查
    for overlap in (0..=n1.min(n2)).rev() {
        if s1[n1 - overlap..] == s2[..overlap] {
            let mut result = s1.to_vec();
            result.extend_from_slice(&s2[overlap..]);
            return String::from_utf8(result).unwrap();
        }
    }

    // 没有重叠，直接拼接
    let mut result = s1.to_vec();
    result.extend_from_slice(s2);
    String::from_utf8(result).unwrap()
}

/// 检查 s2 是否是 s1 的子串
fn contains_substring(s1: &[u8], s2: &[u8]) -> bool {
    if s2.is_empty() {
        return true;
    }
    s1.windows(s2.len()).any(|window| window == s2)
}
```
