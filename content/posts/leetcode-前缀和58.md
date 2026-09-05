---
title: "leetcode-前缀和58"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 字符串中最多数目的子序列

给你一个下标从 0 开始的字符串 text 和另一个下标从 0 开始且长度为 2 的字符串 pattern ，两者都只包含小写英文字母。

你可以在 text 中任意位置插入 一个 字符，这个插入的字符必须是 pattern[0] 或者 pattern[1] 。注意，这个字符可以插入在 text 开头或者结尾的位置。

请你返回插入一个字符后，text 中最多包含多少个等于 pattern 的 子序列 。

子序列 指的是将一个字符串删除若干个字符后（也可以不删除），剩余字符保持原本顺序得到的字符串。


```
impl Solution {
    pub fn maximum_subsequence_count(text: String, pattern: String) -> i64 {
        let chars: Vec<char> = text.chars().collect();
        let (a, b) = (pattern.chars().nth(0).unwrap(), pattern.chars().nth(1).unwrap());

        let mut count_a = 0;      // 已经出现的 pattern[0] 的数量
        let mut count_b = 0;      // 已经出现的 pattern[1] 的数量
        let mut subsequences = 0; // 当前已有的 "ab" 子序列数量

        // 第一遍扫描：计算原始 text 中的 "ab" 子序列数量
        for &c in &chars {
            if c == b {
                // 每个 b 可以跟之前所有的 a 组成子序列
                subsequences += count_a;
                count_b += 1;
            }
            if c == a {
                count_a += 1;
            }
        }

        // 插入一个字符后，最多能增加 min(count_a, count_b) 个新子序列
        // 插入 a 会增加 count_b 个（放在所有 b 前面），插入 b 会增加 count_a 个（放在所有 a 后面）
        // 取两者最大值
        subsequences += count_a.max(count_b);

        subsequences
    }
}
```
