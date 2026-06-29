---
title: "leetcode-分治21"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 最长的美好子字符串


当一个字符串 s 包含的每一种字母的大写和小写形式 同时 出现在 s 中，就称这个字符串 s 是 美好 字符串。比方说，"abABB" 是美好字符串，因为 'A' 和 'a' 同时出现了，且 'B' 和 'b' 也同时出现了。然而，"abA" 不是美好字符串因为 'b' 出现了，而 'B' 没有出现。

给你一个字符串 s ，请你返回 s 最长的 美好子字符串 。如果有多个答案，请你返回 最早 出现的一个。如果不存在美好子字符串，请你返回一个空字符串。

```
impl Solution {
    /// 找出最长的“好子串”
    ///
    /// 好子串定义：子串中的每个字母，其大小写形式都同时出现在该子串中
    /// 例如："aAaB" 不是好子串，因为 'B' 缺少小写 'b'；"aAbB" 是好子串
    ///
    /// 算法思路（分治法）：
    /// 1. 找出当前字符串中所有不同字符，存入 HashSet
    /// 2. 遍历字符串，找到第一个“坏字符”（缺少对应大小写形式的字符）
    /// 3. 以该坏字符为分割点，递归处理左右两部分
    /// 4. 返回较长的那部分
    /// 5. 如果没有坏字符，说明整个字符串本身就是好子串，直接返回
    pub fn longest_nice_substring(mut s: String) -> String {
        use std::collections::HashSet;

        // 收集当前字符串中的所有不同字符
        let char_set: HashSet<char> = s.chars().collect();

        // 遍历字符串，寻找第一个“坏字符”
        for (i, ch) in s.chars().enumerate() {
            // 如果当前字符的大小写形式都存在，则为好字符，继续检查下一个
            if char_set.contains(&ch.to_ascii_uppercase()) && char_set.contains(&ch.to_ascii_lowercase()) {
                continue;
            }

            // 找到坏字符：以当前位置 i 为分割点
            // 递归处理左半部分 [0, i) 和右半部分 [i+1, end)
            let left_part = Solution::longest_nice_substring(s[0..i].to_string());
            let right_part = Solution::longest_nice_substring(s[i + 1..].to_string());

            // 返回长度较长的那一部分
            return if left_part.len() >= right_part.len() { left_part } else { right_part };
        }

        // 没有找到坏字符，整个字符串都是好子串
        s
    }
}
```
