---
title: "leetcode-枚举74"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 从盒子中找出字典序最大的字符串 I

给你一个字符串 word 和一个整数 numFriends。

Alice 正在为她的 numFriends 位朋友组织一个游戏。游戏分为多个回合，在每一回合中：

word 被分割成 numFriends 个 非空 字符串，且该分割方式与之前的任意回合所采用的都 不完全相同 。

所有分割出的字符串都会被放入一个盒子中。

在所有回合结束后，找出盒子中 字典序最大的 字符串。


```
impl Solution {
    pub fn answer_string(word: String, num_friends: i32) -> String {
        // 特殊情况：如果只有一个朋友，整个字符串就是唯一的答案
        if num_friends == 1 {
            return word;
        }

        let n = word.len();
        let max_sub_len = n - num_friends as usize + 1; // 每个分割字符串的最大可能长度
        let mut answer = String::new();

        // 枚举所有可能的子串起始位置
        // 每个子串长度至少为 max_sub_len，因为需要分成 num_friends 个非空字符串
        for start in 0..=n - 1 {
            // 计算当前子串的结束位置（确保不超过字符串长度）
            let end = std::cmp::min(start + max_sub_len, n);
            let candidate = &word[start..end];

            // 保留字典序最大的子串
            if candidate > &answer {
                answer = candidate.to_string();
            }
        }

        answer
    }
}
```
