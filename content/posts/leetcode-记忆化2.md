---
title: "leetcode-记忆化2"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 单词拆分

给你一个字符串 s 和一个字符串列表 wordDict 作为字典。如果可以利用字典中出现的一个或多个单词拼接出 s 则返回 true。

注意：不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。

```
use std::collections::HashSet;

impl Solution {
    /// 单词拆分（Word Break）
    /// 判断字符串 `s` 能否被拆分成字典 `word_dict` 中的单词。
    ///
    /// 动态规划定义：`can_break[i]` 表示字符串 `s[0..i]` 能否被成功拆分。
    /// 转移方程：`can_break[i] = true` 当且仅当存在 `j < i` 使得
    /// `can_break[j] == true` 且 `s[j..i]` 在字典中。
    ///
    /// 函数式优化亮点：
    /// - 使用 `any` 惰性迭代器，自动短路，无需手动 `break`。
    /// - 利用 `HashSet` 直接通过 `&str` 查询，避免额外 `String` 分配。
    /// - 保持纯函数式数据处理风格（迭代器 + 高阶函数）。
    pub fn word_break(s: String, word_dict: Vec<String>) -> bool {
        let text = s;                       // 原字符串
        let n = text.len();                 // 字符串长度
        let word_set: HashSet<String> = word_dict.into_iter().collect(); // O(m) 预处理

        // dp[i] 表示前 i 个字符（即 text[0..i]）是否可拆分
        let mut can_break = vec![false; n + 1];
        can_break[0] = true;                // 空字符串总是可拆分

        // 依次计算每个位置 i = 1..=n
        for i in 1..=n {
            // 函数式风格：使用 any 检查是否存在合法的拆分点 j
            // any 会在找到第一个 true 时立即停止，等价于原代码中的 break
            let has_valid_split = (0..i).any(|j| {
                can_break[j] && word_set.contains(&text[j..i])
            });
            if has_valid_split {
                can_break[i] = true;
            }
        }

        can_break[n]  // 最终结果：整个字符串是否可拆分
    }
}
```
